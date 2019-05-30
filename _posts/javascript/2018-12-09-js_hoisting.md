---
layout: post
title: "Javascript - 호이스팅의 이해 (ES6)"
category: "javascript"
tag:
 - javascript
---



_호이스팅이 어떻게 일어나는 지에 대해 알아본 내용으로, 페이스북 생활코딩 그룹에서 받았던 답변을 바탕으로 ES6 명세를 공부하여 정리한 내용입니다. 사실은 제가 이해하기에 벅찬 내용인데, 혹시나 같은 궁금증을 가진 분들이 계셔서 인터넷을 검색하면 조금이나마 도움이 되었으면 하는 바람에서 글을 적습니다._  

**_혹시 잘못된 내용이 있으면 지적해주시면 감사하겠습니다._**




#### **호이스팅이란**


W3SCHOOL의 설명 (https://www.w3schools.com/js/js_hoisting.asp)  

> Hoisting is JavaScript's default behavior of moving declarations to the top.  





하지만 MDN 에는 이렇게 나와있습니다. (https://developer.mozilla.org/ko/docs/Glossary/Hoisting)    


> 호이스팅(hoisting)은 ECMAScript® 2015 언어 명세 및 그 이전 표준 명세에서 사용된 적이 없는 용어입니다. 호이스팅은 JavaScript에서 실행 콘텍스트(특히 생성 및 실행 단계)가 어떻게 동작하는가에 대한 일반적인 생각으로 여겨집니다. 하지만 호이스팅은 오해로 이어질 수 있습니다. 예를 들어, 호이스팅을 변수 및 함수 선언이 물리적으로 작성한 코드의 상단으로 옮겨지는 것으로 가르치지만, 실제로는 그렇지 않습니다. 변수 및 함수 선언은 컴파일 단계에서 메모리에 저장되지만, 코드에서 입력한 위치와 정확히 일치한 곳에 있습니다.  




그러니까 실제로는 코드가 옮겨지는 것은 아닙니다. 그렇다면 자바스크립트가 어떻게 작동하길래 이게 가능한 걸까요.

먼저 Execution Context를 이해하면 자바스크립트의 엔진의 작동 방식을 이해하는 데 도움이 됩니다.  
Execution Context에 대한 내용은 아래 링크를 참고해주세요.


- 실행 컨텍스트와 자바스크립트의 동작 원리 (https://.com/js-execution-context)
- JavaScript Advanced Tutorials - 5번~10번 동영상
(https://www.youtube.com/playlist?list=PLz1XPAFf8IxbIU78QL158l_KlN9CvH5fg)


**참고**  
위의 두 링크 모두 호이스팅에 대한 자세한 설명을 포함하고 있습니다. 둘 모두 ES6는 아니지만, 제 생각에는 위의 내용으로 호이스팅에 대한 충분한 이해가 되었다면 굳이 더 깊게 알아보지 않아도 괜찮을 것 같습니다.


#### **Specification**

호이스팅을 이해하기 위해서는 명세(Specification)를 찾아볼 수 밖에 없습니다.
아래의 모든 표, 코드 등의 출처는 표기가 없는 한 아래의 명세입니다.
(2018년 12월 9일 기준)

 - **ECMAScript® 2019 Language Specification** (https://tc39.github.io/ecma262/)


#### **ES6의 Execution Context**

ES6에서 Execution Context가 이전과 조금 달라졌습니다.

![]({{ site.url }}/assets/javascript/table21_EC1.png)
![]({{ site.url }}/assets/javascript/table22_EC2.png)       



#### **작동 순서**

코드가 해석되고 실행되는 과정은 Execution Context의 생성과 실행으로 이해할 수 있는데요, 사실은 간단한 두 스텝으로 이루어진 것이 아니라 자바스크립트 엔진의 여러 연산을 거쳐서 생성과 실행 과정이 이루어지게 됩니다.


**호이스팅과 관련이 있는 자바스크립트 코드 해석 순서**  

아래 순서는 시간 순서가 아니라, 오른쪽에 있는 연산이 왼쪽 연산에 포함되어 있는 방식입니다. 예를 들어, RunJobs 안에 ScriptEvaluationJob의 실행이 포함되어 있습니다.    

* RunJobs -> ScriptEvaluationJob -> ScriptEvaluation -> GlobalDeclarationInstantiation 에서
  - 함수는 CreateGlobalFunctionBinding을 통해서 전역 객체에 정의
  - 변수는 CreateGlobalVarBinding을 통해서 전역 객체에 정의
* ScriptEvaluation 에서 GlobalDeclarationInstantiation이후에 작성 코드가 실행됨

#### **설명**


RunJobs의 InitializeHostDefinedRealm에서 새로운(null) Execution Context를 생성한 후, 코드를 해석하고 실행하기 위해 ScriptEvaluation이라는 추상연산이 작동이 됩니다. 아래는 이 추상연산의 전체 내용입니다.

![]({{ site.url }}/assets/javascript/15.1.10 ScriptEval.png)    



여기서 11번에 Let result be GlobalDeclarationInstantiation(scriptBody, globalEnv).가 있는데요,  GlobalDeclarationInstantiation은 이름 그대로, 선언된 전역 함수와 전역 변수를 객체화하고 초기화하는 포함된 추상 연산입니다.  


GlobalDeclarationInstantiation 연산에서 CreateGlobalFunctionBinding와 CreateGlobalVarBinding 두 연산이 각각 전역 함수와 변수를 초기화하고 바인딩합니다. 아래는 GlobalDeclarationInstantiation 추상연산의 일부입니다. 해당 부분만 짤라왔습니다. (17, 18번)

![]({{ site.url }}/assets/javascript/15.1.11_globaldeclara.png)  


#### **CreateGlobalFunctionBinding**


전역 Environment Record의 [[ObjectRecord]]에 담아놨던 functionsToInitialize의 바인딩을 생성하고 초기화합니다. Object Environment Record의 함수 객체에 함수의 내용(PropertyDescriptor) 및 바인딩된 이름이 저장됩니다. 해당하는 Global Object에는 **function**에 대한 속성 값이 저장됩니다. 함수가 실행되기 전에 바인딩을 생성하는 이 단계가 함수 호이스팅을 가능하게 합니다.


#### **CreateGlobalVarBinding**

전역 Environment Record의 [[ObjectRecord]]에 담아놨던 declaredVarNames의 바인딩을 생성하고 undefined로 초기화합니다. 해당하는 Global Object에는 **var**에 대한 속성 값이 저장됩니다. 함수가 실행되기 전 변수를 초기화하는 이 단계가 변수 호이스팅을 가능하게 합니다.



#### **다시 ScriptEvaluation**

ScriptEvaluation 연산에서, 지금까지 살펴본 GlobalDeclarationInstantiation가 끝나고나면 12번 줄에서 이제 코드가 실제로 실행이 됩니다.

```
11. Let result be GlobalDeclarationInstantiation(scriptBody, globalEnv).
12. If result.[[Type]] is normal, then
    Set result to the result of evaluating scriptBody.
```

#### **정리**

1. ScriptEvaluation이 시작되어 코드를 읽기 시작하면
2. GlobalDeclarationInstantiation에서 먼저 전역 스코프에 있는 변수와 함수 선언을 모아 미리 정의해주고
3. 이후에 코드를 실행하기 때문에 호이스팅이 일어남.



#### **후기**

CreateGlobalFunctionBinding과 CreatGlobalVarBinding의 하위 연산은 아직 이해가 되지 않는 부분이 있습니다. 혹시 더 알게 되면 내용을 추가하도록 하겠습니다. 잘못된 것이 있다면 마음껏 지적해주세요. 감사합니다.
