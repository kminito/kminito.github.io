---
layout: post
title: "마크다운(markdown) 들여쓰기 사용하기"
category: jekyll
tag: markdown
---


#### **마크다운 들여쓰기 사용하기**

```
마크다운은 기본적으로 들여쓰기를 지원하지 않습니다. 다만 마크다운 내부에서의 HTML을 허용하고 있으므로,
공백 문자인 '&nbsp;'를 문단의 시작에 입력함으로써 들여쓰기를 적용할 수 있습니다.  
```  
___

<br>
### 예시
#### 즐거운 편지 / 황동규

  내 그대를 생각함은 항상 그대가 앉아 있는 배경에서 해가 지고 바람이 부는 일처럼 사소한 일일 것이나 언젠가 그대가 한없이 괴로움 속을 헤매일 때에 오랫동안 전해 오던 그 사소함으로 그대를 불러 보리라.  

  진실로 진실로 내가 그대를 사랑하는 까닭은 내 나의 사랑을 한없이 잇닿은 그 기다림으로 바꾸어 버린 데 있었다. 밤이 들면서 골짜기엔 눈이 퍼붓기 시작했다. 내 사랑도 어디쯤에선 반드시 그칠 것을 믿는다. 다만 그 때 내 기다림의 자세를 생각하는 것뿐이다. 그 동안에 눈이 그치고 꽃이 피어나고 낙엽이 떨어지고 또 눈이 퍼붓고 할 것을 믿는다.  


___
<br>
위의 글을 보면 들여쓰기가 적용이 안 되어있습니다.  
아래와 같이 문단의 시작에 `&nbsp;&nbsp;&nbsp;&nbsp;`를 입력하면 공백이 4개가 들어가게 됩니다.  

<br>
**코드**

```sh
&nbsp;&nbsp;&nbsp;&nbsp;내 그대를 생각함은 항상 그대가 앉아 있는 배경에서 해가 지고 바람이 부는 일처럼 사소한 일일 것이나 언젠가 그대가 한없이 괴로움 속을 헤매일 때에 오랫동안 전해 오던 그 사소함으로 그대를 불러 보리라.

&nbsp;&nbsp;&nbsp;&nbsp;진실로 진실로 내가 그대를 사랑하는 까닭은 내 나의 사랑을 한없이 잇닿은 그 기다림으로 바꾸어 버린 데 있었다. 밤이 들면서 골짜기엔 눈이 퍼붓기 시작했다. 내 사랑도 어디쯤에선 반드시 그칠 것을 믿는다. 다만 그 때 내 기다림의 자세를 생각하는 것뿐이다. 그 동안에 눈이 그치고 꽃이 피어나고 낙엽이 떨어지고 또 눈이 퍼붓고 할 것을 믿는다.  
```  

<br>
**결과**

&nbsp;&nbsp;&nbsp;&nbsp;내 그대를 생각함은 항상 그대가 앉아 있는 배경에서 해가 지고 바람이 부는 일처럼 사소한 일일 것이나 언젠가
 그대가 한없이 괴로움 속을 헤매일 때에 오랫동안 전해 오던 그 사소함으로 그대를 불러 보리라.

&nbsp;&nbsp;&nbsp;&nbsp;진실로 진실로 내가 그대를 사랑하는 까닭은 내 나의 사랑을 한없이 잇닿은 그 기다림으로 바꾸어 버린 데 있었다.
 밤이 들면서 골짜기엔 눈이 퍼붓기 시작했다. 내 사랑도 어디쯤에선 반드시 그칠 것을 믿는다. 다만 그 때 내 기다림의 자세를 생각하는 것뿐이다.
 그 동안에 눈이 그치고 꽃이 피어나고 낙엽이 떨어지고 또 눈이 퍼붓고 할 것을 믿는다.   

 <br>

참고 위키피디아 - [줄 바꿈 없는 공백](https://ko.wikipedia.org/wiki/%EC%A4%84_%EB%B0%94%EA%BF%88_%EC%97%86%EB%8A%94_%EA%B3%B5%EB%B0%B1)