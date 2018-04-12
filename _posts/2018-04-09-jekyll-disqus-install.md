---
layout: post
title: "Jekyll에 Disqus 설치하기 (minima theme)"
category: jekyll
---

__테스트 게시물__  
### 목표 ###
```
minima theme을 사용하고 있는 jekyll 블로그에 Disqus를 활성화
```



#### 아래와 같은 순서로 이루어집니다.
1. Disqus 회원가입
2. Jekyll의 `_config.yml` 에 `disqus_shortname` 추가
3. `_include\disqus_comments` 파일의 `jekyll.environment == "production"` 삭제

***




### 1. Disqus 가입하기


자세한 내용은 생략


***



### 2. Jekyll의 `_config.yml`에 `shortname` 추가


`_config.yml` 파일에 아래와 같은 내용을 추가합니다.
`readme.md` 파일 참고


```sh
disqus:
  shortname: kminito  #당신의 disqus short name으로 바꾸어주세요
```
disqus shortname 확인하는 법 : [여기 클릭](https://help.disqus.com/customer/portal/articles/466208)

***


### 3. _include\disqus_comments 파일의 조건문 수정

조건문 중 `and jekyll.environment == "production"` 삭제
즉 아래 코드처럼 되도록

{% highlight ruby %}
if page.comments != false
{% endhighlight %}

환경변수를 production으로 바꾸는 것에 실패해서 결국 저 조건문을 삭제하기에 이르렀습니다.

윈도우에서 `JEKYLL_ENV=production`으로 만드는 법을 아시는 분은 좀 알려주세요..


***


## 결론

최신 버전의 minima에서는 결국 1) `_config.yml`에 `shortname`을 추가하고, 2) `disqus_comments`파일의 조건문 중
 `jekyll.environment == "production"` 조건을 제거하면 작성 포스트에 disqus 공간이 자동으로 생깁니다. 물론 front matter 에서 comments : false를 주면 그 게시물은 disqus가 안 뜹니다.

 disqus 가이드에 있는 `Universal Embed Code`를 따로 붙여넣기 하지 않아도 되는 이유는 그 코드가 이미 `disqus_comments` 파일에 들어있기 때문입니다. shortname만 잘 적어주면 됩니다.


```
 이 게시물은 작성중입니다. 깃이라는 게 익숙하지 않아서 이렇게 커밋을 많이 해도 되는 건지..
```
