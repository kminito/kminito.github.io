---
layout: post
title: "마작 짝 맞추기 봇 만들기 (1)"
category: python
tag: python
---


### 개요

아래와 같은 마작 게임을 자동으로 플레이하는 봇 만들기
![짝 맞추기 마작]({{ site.url }}/assets/python/shanghai_1.jpg)


### 현재까지 구현된 기능
프로그램을 실행하고 특정 패 위에 마우스를 놓으면, 화면에서 같은 패를 찾아 새로운 창에서 표시해줌  
다음 게시물에서는 자동으로 패를 인식하고 같은 패를 클릭해주는 기능을 넣고 싶음  


**사용된 기능과 모듈 등**
```
- 마우스 좌표 읽어오기 : pynput.mouse - Controller().position
- 마우스가 위치한 곳의 그림 및 화면 읽기 : ImageGrab.grab
- 같은 패 찾기 : cv2.matchTemplate
- 화면에 직사각형으로 표시하기 cv2.rectangle
- 표시한 화면 띄우기 cv2.imshow
```



**실행 화면**
![짝 맞추기 마작_실행후]({{ site.url }}/assets/python/shanghai_2.jpg)


### 전체 코드

```python

from pynput.mouse import Button, Controller
import time
from PIL import ImageGrab
import numpy as np
import cv2

mouse = Controller()


# 현재 마우스의 좌표를 리턴하는 함수
def getposition():
    position = mouse.position
    return(position)


#특정 좌표 값을 인수로 넣으면 그 근처 30x40 픽셀 이미지와 같은 이미지를 화면에서 찾음
def matching(position):

    # 입력 값을 기준으로 (x1,y1) (x2,y2) 값을 지정 - 가로 30, 세로 40의 직사각형
    # 이 직사각형이 우리가 찾을 template이 됨
    x1 = position[0] - 15
    y1 = position[1] - 20
    x2 = position[0] + 15
    y2 = position[1] + 20

    print("current position : ", position)

    # template (찾을 그림)을 지정
    template = ImageGrab.grab(bbox=(x1, y1, x2, y2))
    template = np.array(template)

    # 템플릿을 찾을 화면 지정 (0,0) 부터 (800, 1000) 픽셀까지의 화면
    screen = ImageGrab.grab(bbox=(000, 000, 800, 1000))
    np_screen = np.array(screen)


    # 같은 그림을 찾으면 가로 30, 세로 40 의 직사각형으로 표시
    w, h = 30, 40

    # np_screen 화면에서 template을 찾음, threshold는 0.9
    res = cv2.matchTemplate(np_screen,template,cv2.TM_CCOEFF_NORMED)
    threshold = 0.9

    # 같은 그림을 찾은 곳의 좌표의 값을 loc 변수에 저장
    loc = np.where( res >= threshold)

    # 같은 그림을 찾은 곳에 직사각형 그리기
    for pt in zip(*loc[::-1]):
        cv2.rectangle(np_screen, pt, (pt[0] + w, pt[1] + h), (0,255,255), 2)

    # 같은 그림을 찾은 곳의 픽셀(x, y)을 리스트로 리턴
    int_list = [(pt[0].item(), pt[1].item()) for pt in zip(*loc[::-1])]

    return np_screen, int_list



def main():

        # 그냥 타이머
        print("Starting in 3")
        time.sleep(1)
        print("2")
        time.sleep(1)
        print( "1")
        time.sleep(1)
        print( "Start")

        # 현재 마우스 위치
        position = getposition()
        # 같은 그림을 찾아서 표시
        result = matching(position)[0]

        # click_list는 같은 그림을 찾은 곳을 클릭하기 위해 만든 리스트
        click_list = matching(position)[1]

        cv2.imshow('result', result)
        cv2.waitKey(0)
        cv2.destroyAllWindows()

if __name__ == '__main__':
    main()

```


### 중간 평가

1. 같은 그림을 찾아서 자동으로 클릭하게 하려고 했으나, 같은 그림을 찾을 때 해당 값 근처 픽셀들도 같이 리턴하는 바람에 결과값이 중복되어 같은 그림을 여러번 클릭하는 문제가 있음. list에서 비슷한 값은 지우는 방식으로 개선 예정  
2. 다음 단계에서는 마우스 위치의 값을 불러오는 게 아닌, 자동으로 패를 인식하고 같은 그림을 찾아서 인식 및 클릭하는 방식으로 완전히 자동화 하고자 함. 그러나 화면에서 활성화된 패와 활성화되지 않은 회식 패를 구분해서 인식하는 것을 어떻게 할지 몰라 방법을 찾고 있음.
3. 짜잘하게 이것저것 시도해본 게 두 달짼데 이번에는 처음으로 스스로 함수를 정의해서 사용해 보았음. 간단하게 사용했지만, 아주 큰 무언가를 할 줄 알게 된 것 같아 기분이 좋음.
