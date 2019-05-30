---
layout : post
title : "파이썬 마우스 좌표 확인"
comments : true
category : python
tag : python
---



파이썬으로 마우스 커서의 위치(좌표)를 확인하기 위한 코드입니다.

#### **소스 코드**
```python
from pynput.mouse import Controller
import time

while(True):
    print("Current position: " + str(Controller().position))
    time.sleep(1)
```


#### **사용**
우선 pynput 모듈이 설치가 되어 있어야겠죠?  
윈도우 기준 cmd에서 `pip install pynput`을 입력하여 pynput을 설치해주세요.

이후 위의 코드 작성하셔서 사용하시면 됩니다.  
코드 실행 중에는 마우스 좌표를 1초마다 읽어서 화면에 띄웁니다.  


`Controller()`의 `position` 메소드가 마우스의 좌표를 리턴하며,  
`time` 모듈은 일부러 딜레이를 주기 위해 넣었습니다.



아래 링크에서 마우스, 키보드의 입력 컨트롤 방법이 잘 나와있습니다.
더 많은 정보가 필요하시면 참고하세요.
[https://pypi.python.org/pypi/pynput](https://pypi.python.org/pypi/pynput)
