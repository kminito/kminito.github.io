---
layout: post
title: "Jekyll blog - 코드블록 글씨체 바꾸기"
category: jekyll
---

jekyll 블로그 코드블럭의 글씨체를 아래와 같이 이쁘게 변경하고자 합니다.

#### 변경 전
![font_before]({{ site.url }}/assets/jekyll/code_font_1.jpg)


#### 변경 후
![font_after]({{ site.url }}/assets/jekyll/code_font_2.jpg)


___
___

#### **방법**
저는 초보라 이거 글씨체 바꾸는 법 알아내는 데 3일 정도가 걸렸습니다. 덕분에 CSS에 대해서도 좀 알게 되었구요.  

단순하게는 코드에 들어가는 스타일에 글씨체를 지정하면 되는되요, 제가 한 순서는 아래와 같습니다.

1. 일단 웹에서 페이지 소스를 확인해서 코드블록이 어느 태그안에 들어가 있는지 확인했구요, 찾아보니 `<pre>` 안에 `<code>`에 들어가있더라구요.

2. 그 다음엔 Jekyll 블로그의 scss 파일을 뒤져서 저 코드의 스타일이 들어있는 곳을 찾았습니다. 찾아보니 `_base.scss` 파일에 있었습니다.

3. 그래서 그곳, 즉 `pre, code { }` 에다가 아래의 코드를 추가했습니다.  
  ```
  font-family: "Consolas", 'Nanum Gothic', "Menlo", "Monaco", "Courier", monospace;
  ```

4. 그랬더니 전체적으로는 아래와 같은 코드가 되었구요. 이러면 끝입니다. `font-family`에 있는 글꼴들은 앞에서 부터 순차적으로 적용이 됩니다. 위의 경우에는 Consolas가 없으면 나눔고딕이, 나눔고딕도 없으면 Menlo가 적용되는 방식입니다.
  ```python
      pre,
        code {
          font-family: "Consolas", 'Nanum Gothic', "Menlo", "Monaco", "Courier", monospace;

          @include relative-font-size(0.9375);
          border: 1px solid $grey-color-light;
          border-radius: 3px;
          background-color: #eef;
      }
  ```


  위의 폰트 중 나눔고딕은 컴퓨터에 기본적으로 설치되어 있지 않은 폰트라, `head.html` 파일에 아래 코드를 추가하여 자동으로 로드(?) 되도록 했습니다.
  ```
  <head>
  ///
    <link rel="stylesheet" href="//fonts.googleapis.com/earlyaccess/nanumgothic.css">
  ///
  </head>
  ```


github 주소 : [https://github.com/kminito/kminito.github.io](https://github.com/kminito/kminito.github.io)
