---
layout: post
title: "스터디 노트 - Udacity HTTP & Web Servers (2) The Web from Python"
category: backend
tag:
  - python
  - web
---

### **Python's http.server**

`http.server`모듈은 HTTP 서버를 작동하기 위한 클래스를 제공한다. 하지만 실제 서버를 운영할때는 사용하면 안 된다. 단지 웹서버 공부나 아니면 기본적인 보안 점검을 위한 용도로만 사용


```py
from http.server import HTTPServer, BaseHTTPRequestHandler

class HelloHandler(BaseHTTPRequestHandler):

    def do_GET(self):
        # First, send a 200 OK response.
        self.send_response(200)

        # Then send headers.
        self.send_header('Content-type', 'text/plain; charset=utf-8')
        self.end_headers()

        # Now, write the response body.
        self.wfile.write("Hello, HTTP!\n".encode())

if __name__ == '__main__':
    server_address = ('', 8000)  # Serve on all addresses, port 8000.
    httpd = HTTPServer(server_address, HelloHandler)
    httpd.serve_forever()

```

설명  
 1) send a 200 OK Status Code  
 2) send headers `send_header`, `end_headers` 사용  
 3) wirtes response body  
  - wfile  = writeable file : 네트워크 커넥션을 파일을 연 것처럼 표현함 (다른 언어도 그렇다고 함). 따라서 wfile은 다 서버에서 클라이언트로의 커넥을 나타낸다. 따라서 `write`를 사용해서 작성한 바이너리 데이터는 클라이언트에게 response로 전송됨  

  - `endcode()`메소드는 스트링을 `bytes` 오브젝트(바이너리 데이터)로 바꾸는 역할  
  - 파이썬은 UTF-8 인코딩이 기본이라 encode()를 그냥 사용하면 UTF-8로 인코딩  



----
### **The echo server**

path에 적은 내용을 텍스트로 보여주는 서버를 만들어보자.  
ex) http://localhost:8000/bears 를 들어가면 화면에 'bears'라는 글자가 보임



`BaseHTTPRequestHandler`의 `path`라는 instance variable이 path 값을 포함하고 있음.  

따라서 위 파일에서 `write`안의 내용을 `self.path.encode()`로 바꾸면 된다. 인코딩을 하지 않을 경우 오류 발생  

```py
self.wfile.write(self.path[1:].encode())
```

---
### **Queries and quoting**

URI에 물음표로 시작하는 부분은 qeury parameter 이다.
```
https://www.google.com/search?q=gray+squirrel&tbm=isch
```
위 내용은 아래의 리퀘스트로 날아간다
```
GET /search?q=gray+squirrel&tbm=isch HTTP/1.1
Host: www.google.com
```

The query part of the URI is the part after the ? mark. Conventionally, query parameters are written as key=value and separated by & signs. So the above URI has two query parameters, q and tbm, with the values gray+squirrel and isch.

There is a Python library called `urllib.parse` that knows how to unpack query parameters and other parts of an HTTP URL. (The library doesn't work on all URIs, only on some URLs.)

_Fragments (샵으로 시작하는 부분) aren't sent to the server as part of an HTTP GET request_

```py
address = 'https://www.google.com/search?q=gray+squirrel&tbm=isch'
from urllib.parse import urlparse, parse_qs
parts = urlparse(address)
print(parts)
```
결과
```py
ParseResult(scheme='https', netloc='www.google.com', path='/search', params='', query='q=gray+squirrel&tbm=isch', fragment='')
```
```py
print(parts.query)
query = parse_qs(parts.query)
query
```
```py
q=gray+squirrel&tbm=isch
{'q': ['gray squirrel'], 'tbm': ['isch']}
```

__HTTP URLs aren't allowed to contain spaces or certain other characters. So if you want to send these characters in an HTTP request, they have to be translated into a "URL-safe" or "URL-quoted" format.__


One of the features of the URL-quoted format is that spaces are sometimes translated into plus signs. Other special characters are translated into hexadecimal codes that begin with the percent sign.

---
### **HTML Form**

HTML form에서 GET method로 아이디와 비밀번호를 전송할 경우 해당 내용이 URL에 포함되어서 날아감.

```HTML
<form action="http://www.google.com/search" method="GET">
        <label>Search term:
            <input type="text" name="q">
        </label>
        <br>
        <label>Corpus:
            <select name="tbm">
                <option selected value="">Regular</option>
                <option value="isch">Images</option>
                <option value="bks">Books</option>
                <option value="nws">News</option>
            </select>
        </label>
        <br>
        <button type="submit">Go go!</button>
    </form>

```
선택한 내용이 쿼리 파라미터로 들어감
```
https://www.google.com/search?q=abcd&tbm=bks
```
---
### **GET and POST**

Idempotence : 여러번 해도 결과 똑같은 것

**POST requests are not idempotent.**


When a browser submits a form as a POST request, it doesn't encode the form data in the URI path, the way it does with a GET request. Instead, it sends the form data in the request body, underneath the headers.

Idempotence : 여러번 해도 결과 똑같은 것  
ex) x = 5

---
### **A server for POST**

GET request에서는 모든 user data가 uri path에 들어가지만 POST request에서는 request body에 있다.

Inside do_POST, our code can read the request body by calling the `self.rfile.read` method. `self.rfile` is a file object, like the `self.wfile` we saw earlier — but `rfile` is for reading the request, rather than writing the response.

However, `self.rfile.read` needs to be told how many bytes to read … in other words, how long the request body is.

Messageboard 예제 (get과 post 모두 포함)

```py
from http.server import HTTPServer, BaseHTTPRequestHandler
from urllib.parse import parse_qs

form = '''<!DOCTYPE html>
  <title>Message Board</title>
  <form method="POST" action="http://localhost:8000/">
    <textarea name="message"></textarea>
    <br>
    <button type="submit">Post it!</button>
  </form>
'''


class MessageHandler(BaseHTTPRequestHandler):
    def do_POST(self):
        # How long was the message?
        length = int(self.headers.get('Content-length', 0))

        # Read the correct amount of data from the request.
        data = self.rfile.read(length).decode()

        # Extract the "message" field from the request data.
        message = parse_qs(data)["message"][0]

        # Send the "message" field back as the response.
        self.send_response(200)
        self.send_header('Content-type', 'text/plain; charset=utf-8')
        self.end_headers()
        self.wfile.write(message.encode())

    def do_GET(self):
        # First, send a 200 OK response.
        self.send_response(200)

        # Then send headers.
        self.send_header('Content-type', 'text/html; charset=utf-8')
        self.end_headers()

        # Then encode and send the form.
        self.wfile.write(form.encode())

if __name__ == '__main__':
    server_address = ('', 8000)
    httpd = HTTPServer(server_address, MessageHandler)
    httpd.serve_forever()
```
---
### **Post-Redirect-Get**
위의 예제에서 Post 후에 메시지를 저장하고 저장된 페이지로 Redirect를 해보자

```py
#   1. In the do_POST method, send a 303 redirect back to the / page.
#   2. In the do_GET method, put the response together and send it.

from http.server import HTTPServer, BaseHTTPRequestHandler
from urllib.parse import parse_qs

memory = []

form = '''<!DOCTYPE html>
  <title>Message Board</title>
  <form method="POST">
    <textarea name="message"></textarea>
    <br>
    <button type="submit">Post it!</button>
  </form>
  <pre>
{}
  </pre>
'''

class MessageHandler(BaseHTTPRequestHandler):
    def do_POST(self):
        # How long was the message?
        length = int(self.headers.get('Content-length', 0))

        # Read and parse the message
        data = self.rfile.read(length).decode()
        message = parse_qs(data)["message"][0]

        # Escape HTML tags in the message so users can't break world+dog.
        message = message.replace("<", "&lt;")

        # Store it in memory.
        memory.append(message)

        # Send a 303 back to the root page
        self.send_response(303)  # redirect via GET
        self.send_header('Location', '/')
        self.end_headers()

    def do_GET(self):
        # First, send a 200 OK response.
        self.send_response(200)

        # Then send headers.
        self.send_header('Content-type', 'text/html; charset=utf-8')
        self.end_headers()

        # Send the form with the messages in it.
        mesg = form.format("\n".join(memory))
        self.wfile.write(mesg.encode())
# 후략
```


---
### **Making Requests**
The `requests` library is a Python library for sending requests to web servers and interpreting the responses.

```py
requests.get("http://localhost:8000/")
```


```
>>> import requests
>>> a = requests.get('http://www.udacity.com')
>>> a
<Response [200]>
>>> type(a)
<class 'requests.models.Response'>
```
r의 reponse body에 접근하기 위해서는, r.text, r.content 사용

Both, but they're different. r.content is a bytes object representing the literal binary data that the server sent. r.text is the same data but interpreted as a str object, a Unicode string.

---
### **Using a JSON API**
JSON is a data format based on the syntax of JavaScript, often used for web-based APIs. There are a lot of services that let you send HTTP queries and get back structured data in JSON format.

Python has a built-in `json` module; and as it happens, the `requests` module makes use of it. A `Response` object has a .json method; if the response data is JSON, you can call this method to translate the JSON data into a Python dictionary.

```py
r = requests.get("http://uinames.com/api?ext&region=United%20States",
                 timeout=2.0)
j = r.json()
```

---
### **Exercise : The Bookmark server**


on GET
 - 루트로 GET 하면 HTML form을 보여준다.
 - 두개의 칸이 있는데 하나는 긴 URI, 다른 하나는 사용하고 싶은 짧은 URI. Submit 버튼은 Post    

on POST
 - 1) requests.get으로 실제 Long URI 값이 들어왔는지 확인(200 뜨는지)
 - 2) Long URI가 있으면 short를 long에 맵핑하여 딕셔너리로 저장하고 short uri로의 링크를 화면에 띄움
 - 3) Long URI가 이상하면 404
 - 4) 둘 중 하나가 입력이 안 되면 400
이미 만들어진 short URI로 온 GET은 long URI로 리다이렉트

```py
 #!/usr/bin/env python3
 #
 # A *bookmark server* or URI shortener.

 import http.server
 import requests
 from urllib.parse import unquote, parse_qs

 memory = {}

 form = '''<!DOCTYPE html>
 <title>Bookmark Server</title>
 <form method="POST">
     <label>Long URI:
         <input name="longuri">
     </label>
     <br>
     <label>Short name:
         <input name="shortname">
     </label>
     <br>
     <button type="submit">Save it!</button>
 </form>
 <p>URIs I know about:
 <pre>
 {}
 </pre>
 '''


 def CheckURI(uri, timeout=5):
     '''Check whether this URI is reachable, i.e. does it return a 200 OK?

     This function returns True if a GET request to uri returns a 200 OK, and
     False if that GET request returns any other response, or doesn't return
     (i.e. times out).
     '''
     try:
         r = requests.get(uri, timeout=timeout)
         # If the GET request returns, was it a 200 OK?
         return r.status_code == 200
     except requests.RequestException:
         # If the GET request raised an exception, it's not OK.
         return False


 class Shortener(http.server.BaseHTTPRequestHandler):
     def do_GET(self):
         # A GET request will either be for / (the root path) or for /some-name.
         # Strip off the / and we have either empty string or a name.
         name = unquote(self.path[1:])

         if name:
             if name in memory:
                 # We know that name! Send a redirect to it.
                 self.send_response(303)
                 self.send_header('Location', memory[name])
                 self.end_headers()
             else:
                 # We don't know that name! Send a 404 error.
                 self.send_response(404)
                 self.send_header('Content-type', 'text/plain; charset=utf-8')
                 self.end_headers()
                 self.wfile.write("I don't know '{}'.".format(name).encode())
         else:
             # Root path. Send the form.
             self.send_response(200)
             self.send_header('Content-type', 'text/html')
             self.end_headers()
             # List the known associations in the form.
             known = "\n".join("{} : {}".format(key, memory[key])
                               for key in sorted(memory.keys()))
             self.wfile.write(form.format(known).encode())

     def do_POST(self):
         # Decode the form data.
         length = int(self.headers.get('Content-length', 0))
         body = self.rfile.read(length).decode()
         params = parse_qs(body)

         # Check that the user submitted the form fields.
         if "longuri" not in params or "shortname" not in params:
             self.send_response(400)
             self.send_header('Content-type', 'text/plain; charset=utf-8')
             self.end_headers()
             self.wfile.write("Missing form fields!".encode())
             return

         longuri = params["longuri"][0]
         shortname = params["shortname"][0]

         if CheckURI(longuri):
             # This URI is good!  Remember it under the specified name.
             memory[shortname] = longuri

             # Serve a redirect to the form.
             self.send_response(303)
             self.send_header('Location', '/')
             self.end_headers()
         else:
             # Didn't successfully fetch the long URI.
             self.send_response(404)
             self.send_header('Content-type', 'text/plain; charset=utf-8')
             self.end_headers()
             self.wfile.write(
                 "Couldn't fetch URI '{}'. Sorry!".format(longuri).encode())

 if __name__ == '__main__':
     server_address = ('', 8000)
     httpd = http.server.HTTPServer(server_address, Shortener)
     httpd.serve_forever()
 ```
