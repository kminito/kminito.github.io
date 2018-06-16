---
layout: post
title: "마크다운(markdown) 유튜브 동영상 넣기 "
category: jekyll
tag:
  - jekyll
  - markdown
---

아래와 같이 마크다운 문서에 유투브 영상을 삽입하고자 합니다

<iframe width="640" height="360" src="https://www.youtube.com/embed/kTcRRaXV-fg?ecver=1" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>  




#### **방법**
아래 그림과 같이 유튜브 동영상에서 마우스 오른쪽을 클릭하여 소스를 복사, 마크다운에 적용  
(HTML의 ```<iframe>``` 태그를 사용 -
[참고  : https://www.w3schools.com/tags/tag_iframe.asp](https://www.w3schools.com/tags/tag_iframe.asp))


>
![유투브동영상소스복사]({{ site.url }}/assets/jekyll/markdown_video.jpg)  



**복사한 소스코드**


```html
<iframe width="640" height="360" src="https://www.youtube.com/embed/kTcRRaXV-fg?ecver=1"  
 frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>  
```



#### **사이즈에 관하여**
```<iframe>``` 태그의 ```width```와 ```height```는 픽셀 값을 인자로 받음. 따라서 태그 자체의 속성을 변경해서는 브라우저의 상황에 따라 동영상의 크기가 자동으로 바뀌도록 할 수가 없다.  

대신 CSS를 이용해서 사이즈를 조절하도록 할 수 있음. 아래에서는 iframe에 "youtube"라는 class 이름을 부여하여 div의 크기에 따라 상대적인 크기를 유지하도록 했다.

**CSS 추가 내용**
```css
.youtube{
    width: 100%;
    height: 100%;
}
```


**삽입한 태그**
```html
<iframe class="youtube" src="https://www.youtube.com/embed/kTcRRaXV-fg?ecver=1"  
 frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>  
```

**결과**
<iframe class="youtube" src="https://www.youtube.com/embed/kTcRRaXV-fg?ecver=1" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>   

**코멘트**  

동영상의 세로 길이가 조정이 안 된다. 이유를 모르겠다.
