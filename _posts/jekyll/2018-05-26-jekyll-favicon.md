---
layout: post
title: "Jekyll blog - favicon 설정하기 "
category: jekyll
tag:
  - jekyll
  - favicon
---

블로그에 아래와 같은 favicon을 삽입하고자 합니다  

![favicon_screenshot]({{ site.url }}/assets/jekyll/favicon_1.jpg)


#### **favicon**

**파비콘**(영어: favicon, 'favorites + icon') 또는 **패비콘** 이란 인터넷 웹 브라우저의 주소창에 표시되는 웹사이트나 웹페이지를 대표하는 아이콘이다. [(출처: 위키피디아)](https://ko.wikipedia.org/wiki/%ED%8C%8C%EB%B9%84%EC%BD%98)  




#### **방법**
Jekyll 블로그에서는 기본 폴더에 `favicon.ico` 파일을 넣어주면 자동으로 favicon이 설정이 됩니다.

혹은 다른 위치에 저장된 favicon 파일을 이용하고 싶다면, 해당 레이아웃의 `<head>` 섹션에 아래와 같은 코드를 삽입하는 것으로도 설정이 가능합니다.

```
 <link rel="shortcut icon" href="http://example.com/myicon.ico">
```

이와 관련한 설명은 ['영문 위키피디아'](https://en.wikipedia.org/wiki/Favicon#How_to_use)를 참조하세요.


#### **과정**

1. 저 같은 경우에는 참새 모양의 파비콘을 만들고 싶어서, 일단 구글에 'sparrow icon'을 검색
2. 그대로 갖다 쓰는 건 양심에 찔려서, 참고만 하고 포토샵에서 직접 참새를 그림
3. 사이즈를 32x32 픽셀로 설정하고, `favicon.png` 파일로 저장
4. 해당 파일의 형식을 `.png`에서 `.ico`로 [웹](https://convertio.co/kr/png-ico/)에서 변환
5. 블로그 root directory에 저장 후 로컬에서 확인


#### **기타**
1. 위의 위키피디아에 따르면 인터넷 익스플로러의 경우 `.ico`형식의 파비콘은 익스플로러 버전 5 이상부터 지원하지만, `.png`형식의 파비콘은 11 버전 이상에서만 지원한다고 합니다.
2. 또한 `.png`, `.ico`가 모두 설정이 되어 있을 경우에는 어떤 것을 적용하는지는 브라우저에 따라 다르다고 합니다. 예를 들면 윈도우용 Chrome 에서는 처음으로 작성된 것이 16x16일 경우 그것을 적용하고, 그렇지 않을 경우 사이즈 상관 없이 확장자가 `.ico`인 파일을 적용합니다. Firefox와 Safari 에서는 이와 상관없이 무조건 마지막으로 작성된 코드가 적용이 된다고 하구요.  
3. 저 같은 경우에는 이런 여러가지 옵션을 신경쓰지 않기 위해서 32x32 픽셀 사이즈의 ico 파일을 만들어서 설정했습니다.
