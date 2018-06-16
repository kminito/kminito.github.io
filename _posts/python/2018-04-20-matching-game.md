---
layout: post
title: "짝 맞추기 게임(기억력 게임) 봇"
category: python
tag: python
---

**짝 맞추기 게임의 반자동화 버전입니다.**

[유투브 영상 보기](https://youtu.be/91CLx-vqyV4)

### 개요

짝 맞추기 게임에서 매 라운드를 시작할 때 프로그램을 실행하면, 블록을 하나씩 클릭하여 뒷면의 모양을 확인하고 모든 블록의 클릭이 끝나면 뒷면의 이미지를 모아서 하나의 창으로 보여주는 프로그램입니다. 그러면 그 화면을 보고 짝을 맞추는 것은 직접 해야합니다.



### 코드 작동 순서

1. 모니터 화면을 캡쳐 (100, 000, 800, 1000)
 - imageGrab
2. 캡쳐한 화면에서 temp_web.jpg (클릭할 블록의 모양)와 같은 모양을 찾음
 - cv2.matchTemplate
 - int_list 변수에는 위에서 찾은, 같은 모양의 것들의 좌표가 들어감

3. 같은 모양이 있는 좌표를 두개씩 마우스로 클릭하여 뒷면을 확인
 - Controller의 move, press method

4. 클릭해서 뒷면이 보이면 캡쳐하여 그 부분을 원래 화면(이미지)에 덮어씌움
 - ImageGrab
 - img[x1:x2,y1:y2] = img2[x3:x4,y3:h4]
5. 덮어씌운 새로운 이미지를 화면에 띄움
- cv.2imshow  




**영상 스크린샷**

![font_after]({{ site.url }}/assets/python/jewel1.jpg)  

왼쪽 게임 화면의 블록을 일일이 다 클릭하고 나면 오른쪽의 이미지를 띄워줍니다.  
그러면 오른쪽의 이미지를 보고 왼쪽 게임 화면에서 직접 클릭해서 짝을 맞추면 됩니다.  

<br>

### 전체 코드
  ```python

import cv2
import numpy as np
from PIL import ImageGrab
import time
from pynput.mouse import Button, Controller

# 마우스 클릭용
mouse = Controller()

# 화면 캡쳐
screen = ImageGrab.grab(bbox=(100, 000, 800, 1000))
np_screen = np.array(screen)

# 템플릿을 불러와 매칭, 모양과 색이 같은 부분의 좌표를 넘파이 어레이로 리턴
img_gray = cv2.cvtColor(np_screen, cv2.COLOR_BGR2GRAY)
template = cv2.imread('temp_web.jpg',0)
w, h = template.shape[::-1]
res = cv2.matchTemplate(img_gray,template,cv2.TM_CCOEFF_NORMED)
threshold = 0.95
loc = np.where( res >= threshold)

# numpy array를 int로 된 tuple의 list로 변환
int_list = [(pt[0].item(),pt[1].item()) for pt in zip(*loc[::-1])]

# 한 루프에 두 블록씩 클릭할 수 있도록 두 블록을 하나의 리스트 요소로 만듦
n = len(int_list)
half_n = int(n/2)

click_list=[]

for i in range(1, half_n+1):
    click_list.append((int_list[2*i-2],int_list[2*i-1]))

# 뒷부분에 뒷면 모양 붙여넣기 할 때 튜플이 필요하다고 해서 튜플로도 만듦
click_tuple= [(pt[0].item(),pt[1].item()) for pt in zip(*loc[::-1])]

for click_position in click_list:

    x1 = click_position[0][0]
    y1 = click_position[0][1]
    x2 = click_position[1][0]
    y2 = click_position[1][1]

    # 한 루프의 첫번째 블록 클릭
    mouse.position = (0, 0)
    mouse.move(x1+100,y1)
    time.sleep(0.04)
    mouse.press(Button.left)
    time.sleep(0.04)
    mouse.release(Button.left)
    # 위의 move 좌표에 +100이 들어가는 이유는 모니터 전체 픽셀에서의 위치
    # 를 입력해야 하기 때문임. 처음 ImageGrab이 화면 100만큼 오른쪽부터 시작
    # 했기 때문에 +100을 해줌 (이외 좌표는 모두 캡처한 화면 안에서의 픽셀)

    time.sleep(0.2)

    # 한 루프의 두번째 블록 클릭
    mouse.position = (0, 0)
    mouse.move(x2+100,y2)
    time.sleep(0.04)
    mouse.press(Button.left)
    time.sleep(0.04)
    mouse.release(Button.left)

    mouse.position = (0, 0)
    time.sleep(0.4)

    # 한 루프의 두 블록을 클릭한 후 뒷면 모양을 복사하여
    # 원래 화면인 np_screen에 붙여넣기
    current_screen = np.array(ImageGrab.grab(bbox=(100, 000, 800, 1000)))

    np_screen[y1:y1+w, x1:x1+w] = current_screen[y1:y1+w, x1:x1+w]
    np_screen[y2:y2+w, x2:x2+w] = current_screen[y2:y2+w, x2:x2+w]
    time.sleep(0.8)


# 위의 루프를 돌아서 np_screen에 뒷면 모양이 모두 들어가면
# cv2.imshow로 위의 이미지를 띄움. 이제 이걸 보고 짝을 맞춰야함
while(True):
    cv2.imshow('window', np_screen)

    # 화면 보고 짝 다 맞추고 나면 q를 눌러서 꺼야함..!
    if cv2.waitKey(50) & 0xFF == ord('q'):
        cv2.destroyAllWindows()
        break

  ```


### 총평
무엇보다도 조잡한 코드를 올려서 죄송합니다. 원래 목표는 휴대폰 화면을 컴퓨터에 띄워서 작동하도록 하는 것이었는데,
실제로 해보니 휴대폰이 느려서 잘 작동이 되지 않았습니다. 따라서 이 작은 프로젝트는 여기서 중단되었고..
