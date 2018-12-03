---
layout: post
title: "Javascript - 함수의 Argument(인자) 유무 확인"
category: javascript
tag:
  - javascript
---

**함수의 인자가 주어졌는지 확인하기 위한 if 구문**


```js
var func1 = function (collection, iterator, accumulator) {
  //코드
}
```
위와 같이 세 개의 파라미터를 가진 함수가 정의되어 있다고 하자.

함수가 실행될 때 세 번째 parameter인 accumulator에 값이 입력되었는지 확인하는 방법은 다음과 같다.

```js
    if(accumulator !== undefined){
      //코드
    }
```  


**참고사항**  

아래와 같은 방식을 사용해서는 안 된다. 왜냐하면 accumulator에 "false", "0", "" 등의 falsy한 값이 입력될 경우 실행되지 않기 때문.
```js
    if(accumulator){
      // accumulator에 0이 입력될 경우 이 조건문은 거짓이 되어 실행되지 않음 
    }
```
