---
layout: post
title: "Jekyll에 Disqus 설치하기 (Minima theme)"
categories: Jekyll
---

#### 아래와 같은 순서로 이루어집니다.
1. Disqus 회원가입
2. Jekyll의 _config.yml 에 disqus_shortname 추가
3. _include\disqus_comments 파일의 jekyll.environment == "production" 삭제




#### 1. Disqus 가입하기


자세한 내용은 생략


#### 2. Jekyll의 _config.yml에 disqus_shortname 추가


_config.yml 파일에 아래와 같은 내용을 추가합니다.
readme.md 파일 참고


```sh
disqus:
  shortname: kminito
```

#### 3. _include\disqus_comments 파일의 jekyll.environment == "production" 삭제


환경변수를 production으로 바꾸는 것에 실패해서 결국 저 조건문을 삭제하기에 이르렀습니다.
Ruby Prompt에서 jekyll serve JEKYLL_ENV=production 을 실행하면 될 줄 알았으나 실패함.
