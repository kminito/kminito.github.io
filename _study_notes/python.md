---
layout: note_layout
title: "python"
---


```
문자열 리버스
list = str_num[::-1]
```

```
map()함수와 딕셔너리의 콜라보 (dmap은 요일 표시 딕셔너리)
df['Day of Week'] = df['Day of Week'].map(dmap)
```

```
Virtual Env
```
가상환경 만들기 (biopython을 포함한 snowflake라는 가상환)
conda create --name snowflakes biopython


활성화
activate snowflakes
활성화
decativate

quit() 나가기

가상환경 리스트업
conda info --envs

```
print에서 format 사용

num = 12
name = 'Sam'

print('My number is: {one}, and my name is: {two}'.format(one=num,two=name))
print('My number is: {}, and my name is: {}'.format(num,name))
```
```
set : collection of unique elements
{1,2,3,1,2,1,2,3,3,3,3,2,2,2,1,1,2}
{1, 2, 3}
s = set([1,2,3,1,2,1,2,3,3,3,3,2,2,2,1,1,2])
s.add(5)
```

```
반복되는 문자열 없애고 싶을 땐 replace로 ''로 바꿈
그다음에 abc[1:-1] 등으로 앞 뒤 바꿈

```



```
RGB_img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
cv2.imwrite('agc.jpg', sample)
```

직사각형 그리기

```
cv2.rectangle(img, start, end, color, thickness)
Parameters:
img – 그림을 그릴 이미지
start – 시작 좌표(ex; (0,0))
end – 종료 좌표 (ex; (500. 500))
color – BGR형태의 Color (ex; (255, 0, 0) -> Blue)
thickness (int) – 선의 두께. pixel
```


```
img = cv2.imread('baseball-player.jpg')
ball = img[409:454, 817:884] # img[행의 시작점: 행의 끝점, 열의 시작점: 열의 끝점]
img[470:515,817:884] = ball # 동일 영역에 Copy
cv2.imshow('img', img)
cv2.waitKey(0)>>> cv2.destroyAllWindows()
```

combination
```
from itertools import combinations
```
