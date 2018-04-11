---
layout : post
title : "파이썬 마우스 좌표 확인"
comments : true
---

테스트 게시물입니다.
파이썬으로 마우스 커서의 위치(좌표)를 확인하기 위한 코드입니다.

{% highlight ruby %}
from pynput.mouse import Controller
import time

while(True):
    print("Current position: " + str(Controller().position))
    time.sleep(1)
{% endhighlight %}

Controller()의 position 메소드가 마우스의 좌표를 리턴하며,
time 모듈은 일부러 딜레이를 주기 위해 넣었습니다.

루프에서 나오려면 어떻게 해야 하는지 몰라 그냥 이렇게 사용했습니다.

아래 링크에서 마우스, 키보드의 입력 컨트롤 방법이 잘 나와있습니다.
기회가 되면 정리해서 올려보도록 하겠습니다.

[https://pypi.python.org/pypi/pynput](https://pypi.python.org/pypi/pynput)
