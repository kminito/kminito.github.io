---
layout: post
title: "WeChall Training: Programming 1 - 풀이 (write-up)"
category: hacking
tag:
  - hacking
  - wechall
---

그냥 리퀘스트를 날리면 안 된다. 쿠키를 헤더에 집어넣어야 한다.


크롬의 주소창에 아래 주소를 입력하면 브라우저에 저장된 쿠키를 검색할 수 있다.  
(설정 메뉴에 들어가 직접 찾아갈 수도 있음)
```
chrome://settings/siteData
```
들어가서 wechall 을 검색하면 `WC` 라는 이름의 쿠키가 하나 나온다. 아래 코드처럼 헤더에 쿠키를 넣어주면 된다.  




#### **소스코드**
```python
import requests

url = "https://www.wechall.net/challenge/training/programming1/index.php?action=request"
dest = "http://www.wechall.net/challenge/training/programming1/index.php?answer="

headers = {"Cookie": "WC=11448154-47078-ofY03tkiDdxqgUKX"}
string = requests.get(url, headers=headers).text
print(string)
response = requests.get(dest + string, headers=headers).text
print(response)
```

response에서 문제 풀이 결과를 확인하려면 힘들다.  

wechall 문제 목록으로 돌아가서 이 문제가 풀린 것으로 뜨는지 확인하거나, 아니면 다시 실행할 때 리눅스 기준 파이프로 grep answer 하면 이미 푼 문제라고 뜬다.

```sh
kminito@min-ideapad:~/workspace/wechall$ python3 programming-1.py | grep answer
```

아래와 같은 내용이 포함된 결과를 볼 수 있다.
```
//
	Your answer is correct but you have already solved this challenge.
//
```
