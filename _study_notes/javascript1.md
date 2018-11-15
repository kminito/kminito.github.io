---
layout: note_layout
title : Javascript

---

```js
배열을 합칠 땐

var new_array = old_array.concat([...values])


거듭제곱은
Math.pow(base, exponent)


배열을 자를 땐

arr.slice() arr.slice(begin) arr.slice(begin, end)
: end는 불포함


배열의 깊은 복사를 간단히 하면?
Array.slice()



오브젝트 키를 반환
Object.keys(obj)


배열인지 확인
Array.isArray(obj)




includes () 메서드는 배열에 특정 요소가 포함되어 있는지 여부를 확인하여
 적절하게 true 또는 false를 반환합니다.
arr.includes(searchElement)
arr.includes(searchElement, fromIndex)


str.indexOf(searchValue[, fromIndex])
Array.prototype.indexOf()

var new_array = arr.map(function callback(currentValue[, index[, array]]) {


맵도 새 배열 선언






var words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];
const result = words.filter(word => word.length > 6);
var newArray = arr.filter(callback(element[, index[, array]])[, thisArg])
Array.isArray()

filter는 new array에 할당해서 리턴


```
```js
arr.reduce(callback[, initialValue])callback배열의 각 (요소) 값에 대해 실행할 함수. 인수(argument) 4개를 취하며, 각 인수는 다음과 같습니다  
 accumulator누산기(accumulator)는 콜백의 반환값을 누적합니다. 초기값(initialValue)이 주어진 경우에는 그 값(아래 참조) 또는 콜백의 마지막 호출에서 이전에 반환된 누적값입니다.  

 currentValue배열 내 현재 처리되고 있는 요소.  
 currentIndexOptional배열 내 현재 처리되고 있는 요소의 인덱스.  

 초기값(initialValue)이 주어진 경우 0부터, 그렇지 않으면 1부터 시작합니다.  
```
