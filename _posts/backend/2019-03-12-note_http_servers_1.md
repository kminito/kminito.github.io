---
layout: post
title: "스터디 노트 - Udacity HTTP & Web Servers (1) Requests and Responses"
category: backend
tag:
  - python
  - web
---

## **1. URI**

---

A **server** is just a program that accepts connections from other programs on the network.  
A web address is also called **URI (Uniform Resource Identifier)**  
a **URL (Uniform Resource Locator)** is a URI for a resource on the network.


#### **URI 예시**  
```
https://en.wikipedia.org/wiki/Fish
```   

이 URI 는 세 부분으로 나누어진다  
- 1) _https_ is the **scheme**;
- 2) _en.wikipedia.org_ is the **hostname**;
- 3) _/wiki/Fish_ is the **path**.

다른 URI들은 다른 형태를 지닐 수도 있다.


#### 1) Scheme

tells the client how to go about accessing the resource.  
ex) http, https, file,  
file은 로컬, 나머지 두개는 웹서버용

HTTPS URI는 암호화된 연결을 사용한다.

참고 - URL Scheme 리스트  
http://www.iana.org/assignments/uri-schemes/uri-schemes.xhtml



#### 2) Hostname
In an HTTP URI, the next thing that appears after the scheme is a hostname — something like www.udacity.com or localhost. This tells the client which server to connect to.


웹주소에 보통 scheme와 hostname 사이에 `://`가 있는 이유는 :는 scheme 뒤에 붙고 //는 hostname 앞에 붙기 때문.   
mailto 는 호스트 네임이 없기 때문에 `:` 만 함. ex) mailto:someone@example.com

#### 3) PATH
In an HTTP URI (and many others), the next thing that appears is the path, which identifies a particular resource on a server.


URI를 path 없이 적을 경우 브라우저가 자동으로 기본 path를 만들어줌  
`http://udacity.com` ->`http://udacity.com/`와 같이 자동으로 끝에 슬래시를 붙임

이와 같이 슬러시 한개로 끝나는 path를 root라고 부름

 http://localhost:8000/ -> the root of the resources served by the web server.



---



## **2. Hostnanmes and ports**

#### 호스트네임

Your operating system's network configuration uses the Domain Name Service (DNS) — a set of servers maintained by Internet Service Providers (ISPs) and other network users — to look up hostnames and get back IP addresses.


In the terminal, you can use the host program to look up hostnames in DNS:  
`host google.com` or `nslookup google.com`



IP 주소는 두 종류가 있다. IPv4와 IPv6
IPv4 예시-  127.0.0.1 or 216.58.194.164
IPv6 예시- 2607:f8b0:4005:804::2004 (조금 더 간단히 나타낼 수 있음)



#### Ports

로컬호스트 데모서버에 접속할때는 `http://localhost:8000/` 와 같이 포트 번호를 표시했으나 보통 인터넷을 사용할때는 잘 표시 안함
왜냐하면 scheme을 보면 대충 알 수 있기 때문

예를 들어 http는 보통 80, https는 443번 포트를 사용. 데모서버 같은 경우에는 일반적인 서버가 아니므로 일부러 포트번호를 8000으로 지정한 것임

---
### **HTTP GET Requests**


**server log on terminal**
```
127.0.0.1 - - [03/Oct/2016 15:45:50] "GET /readme.png HTTP/1.1" 200 -
```
-> 날짜시간 옆 부분은 클라이언트의 브라우저가 서버에 보낸 requests를 나타낸다.  
**받은 리퀘스트** : `GET /readme.png HTTP/1.1.`  

`GET`은 리퀘스트를 만들 때 사용된 HTTP Verb를 나타냄.  `GET` 은 서버에 웹페이지나 이미지 같은 리소스를 요청할 때 사용하는 verb다.

`/readme.png`는 요청된 리소스의 path이다.  
클라이언트는 전체 URI를 보내지 않는다는 것을 알아두자.
 `https://localhost:8000/readme.png`처럼 보내지는 않는다.


마지막으로,`HTTP/1.1`는 리퀘스트의 프로토콜이다.
현재까지 HTTP의 작동 방식에도 변화가 있었으므로, 클라이언트는 서버에게 어떤 버전의 HTTP를 사용하는지 말해줄 필요가 있다. 여기서 사용된 `HTTP/1.1`이 현재 가장 보편적인 버전이다.



## **3. HTTP responses**


**연습!** - 구글에 리퀘스트 날리기

**이런 리퀘스트를 날리면**
```
ncat google.com 80
GET/ HTTP/1.1
Host: google.com
```


**이런 리스판스가 돌아온다**
```
HTTP/1.1 301 Moved Permanently
Location: http://www.google.com/
Content-Type: text/html; charset=UTF-8
Date: Sat, 16 Mar 2019 01:44:09 GMT
Expires: Mon, 15 Apr 2019 01:44:09 GMT
Cache-Control: public, max-age=2592000
Server: gws
Content-Length: 219
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN

<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>

```

HTTP response 는 세 부분으로 이루어져 있다.

- 1) the status line
- 2) some headers
- 3) response body.

* 맨 첫 줄은 status line, 이후에 빈 줄이 나올때까지의 모든 내용이 header, 그리고 나머지 모든 내용이 response body이다.


---
#### 1) Status line
In the response you got from your demo server, the status line said HTTP/1.0 200 OK. In the one from Google, it says HTTP/1.1 301 Moved Permanently.

**The status line tells the client whether the server understood the request, whether the server has the resource the client asked for, and how to proceed next. It also tells the client which dialect of HTTP the server is speaking.**  

The numbers 200 and 301 here are HTTP status codes. There are dozens of different status codes. The first digit of the status code indicates the general success of the request. As a shorthand, web developers describe all of the codes starting with 2 as "2xx" codes, for instance — the x's mean "any digit".
```
1xx — Informational. The request is in progress or there's another step to take.
2xx — Success! The request succeeded. The server is sending the data the client asked for.
3xx — Redirection. The server is telling the client a different URI it should redirect to. The headers will usually contain a Location header with the updated URI. Different codes tell the client whether a redirect is permanent or temporary.
4xx — Client error. The server didn't understand the client's request, or can't or won't fill it. Different codes tell the client whether it was a bad URI, a permissions problem, or another sort of error.
5xx — Server error. Something went wrong on the server side.
```
위키피디아에 자세한 설명이 있다. (https://ko.wikipedia.org/wiki/HTTP_%EC%83%81%ED%83%9C_%EC%BD%94%EB%93%9C)


#### 2) Headers


**위에서 가져온 헤더부분**
```
Location: http://www.google.com/
Content-Type: text/html; charset=UTF-8
Date: Sat, 16 Mar 2019 01:44:09 GMT
Expires: Mon, 15 Apr 2019 01:44:09 GMT
Cache-Control: public, max-age=2592000
Server: gws
Content-Length: 219
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
```

Each header is a line that **starts with a keyword**, such as Location or Content-type, followed by a colon and a value.  5
헤더는 브라우저에 표시되지 않지만, 클라이언트에게 reponse에 대한 다양한 정보를 제공하는 일정의 메타데이터다.  
웹의 많은 기능들이 이 헤더를 통해서 작동한다. 브라우저가 쿠키를 사용하는 것도 헤더를 이용한 것임. (`Set-Cookie`헤더)

A `Content-type` header indicates the kind of data that the server is sending. It includes a general category of content as well as the specific format. ex) PNG image file -> Content-type image/png.Content가 text (HTML 포함)이라면 서버는 어떤 인코딩을 사용했는지도 함께 알려준다 (여기선는 UTF-8).

헤더는 response body에 대한 메타 데이터를 담고 있는 경우도 많다. ex) `Content-Length`



#### 3) Response body
헤더가 끝나고 빈 줄 다음 부분은 모두 response body다. 리퀘스트가 성공적이었다면(예를 들어 200 OK status), 이 부분에는 클라이언트가 요청했던 리소스가 들어간다. (뭐 웹페이지, 그림 등 아무거나)

---



#### **직접 response를 보내보자**  

터미널에서 `ncat -l 9999`를 사용하여 9999번 포트를 듣자.  
그리고 브라우저에서 `http://localhost:9999/`를 접속하면, 터미널에서는 리퀘스트를 받아서 표시한다.

브라우저는 아직 리스판스가 오지 않았으므로 기다리는 상태. 이 상황에서 터미널에다가 아래의 리스판스를 직접 작성하면,
```
HTTP/1.1 307 Temporary Redirect
Location: https://www.eff.org/
```

브라우저는 리스판스를 받아서 리다이렉트한다.!
