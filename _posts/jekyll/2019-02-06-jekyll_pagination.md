---
layout: post
title: "Jekyll blog - Pagination"
category: jekyll
tag:
  - jekyll
---

**아래와 같이 블로그 메인 페이지에 Pagination을 삽입하고자 합니다.**

![]({{ site.url }}/assets/jekyll/2019-02-06-jekyll_pagination/1.png)


Jekyll 공식 문서를 참고하여 만듭니다.
([https://jekyllrb-ko.github.io/docs/pagination/](https://jekyllrb-ko.github.io/docs/pagination/))


### 페이지 나누기 설치
`Gemfile`과 `_config.yml`의 plugins에 jekyll-paginate를 추가


__Gemfile__
```ruby
 # pagination
 gem "jekyll-paginate"
```

__\_config.yml__
```ruby
 plugins:
  - jekyll-paginate
```

터미널에서 `bundle install` 및 `bundle exec jekyll build` 실행
```cmd
kminito@min-ideapad:~/workspace/blog/kminito.github.io$ bundle install
kminito@min-ideapad:~/workspace/blog/kminito.github.io$ bundle exec jekyll build
```

### 페이지 나누기 활성화

 `_config.yml` 파일에 한 페이지당 보여줄 목록 개수 및 각 페이지의 주소 설정

__\_config.yml__
 ```ruby
 paginate: 10 # 한 페이지당 10개의 게시물 목록
 paginate_path: "/main/page:num/" #각 페이지의 URL 설정 (페이지 버튼 만들 때 링크 사용)
```



### 페이지네이션 삽입
페이지 나누기는 HTML 파일에서만 작동하므로, index.md를 index.html로 바꾸어야 합니다.  
또한 기존에 Post를 렌더링하는 태그가 home.html 안에 존재하므로, 이것도 index.html로 옮겨줍니다.

__home.html__ 에서

```js
for post in site.posts //템플릿태그 생략
```
위 내용을 아래 내용으로 변경
```js
for post in paginator.posts // 템플릿태그 생략
```  

그리고 위 내용을 포함하여 포스트 제목 등을 렌더링하는 부분 전체를 index.html로 이동

(Syntax Highlight에 Template Tag가 적용되지 않아 스크린샷으로 올립니다. 전체 소스코드는 깃허브에서 확인 가능합니다.)
[https://github.com/kminito/kminito.github.io/blob/9f1f972b672be4fc5b93c0b963ba503e5c1d6e26/index.html](https://github.com/kminito/kminito.github.io/blob/9f1f972b672be4fc5b93c0b963ba503e5c1d6e26/index.html)

__index.html__ 파일의 일부
![]({{ site.url }}/assets/jekyll/2019-02-06-jekyll_pagination/3.png)

클래스는 모두 CSS를 위해 삽입한 것입니다.


### CSS 설정
부트스트랩4 에서 pagination 부분만 잘라왔습니다.


```css
/* CSS for pagination */
.pagination {
  display: flex;
  padding-left: 0;
  list-style: none;
  border-radius: 0.25rem;
}

.pagiation-link {
  position: relative;
  display: block;
  padding: 0.5rem 0.75rem;
  margin-left: -1px;
  line-height: 1.25;
  color: #007bff;
  background-color: #fff;
  border: 1px solid #dee2e6;
}

.pagiation-link:hover {
  z-index: 2;
  color: #0056b3;
  text-decoration: none;
  background-color: #e9ecef;
  border-color: #dee2e6;
}

.pagiation-link:focus {
  z-index: 2;
  outline: 0;
  box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, 0.25);
}

.pagiation-link:not(:disabled):not(.disabled) {
  cursor: pointer;
}

.page-item:first-child .pagiation-link {
  margin-left: 0;
  border-top-left-radius: 0.25rem;
  border-bottom-left-radius: 0.25rem;
}

.page-item:last-child .pagiation-link {
  border-top-right-radius: 0.25rem;
  border-bottom-right-radius: 0.25rem;
}

.page-item.active .pagiation-link {
  z-index: 1;
  color: #fff;
  background-color: #007bff;
  border-color: #007bff;
}

.page-item.disabled .pagiation-link {
  color: #6c757d;
  pointer-events: none;
  cursor: auto;
  background-color: #fff;
  border-color: #dee2e6;
}
```

### 남은 문제
아래와 같이 게시물 숫자가 늘어날 경우 페이지 숫자도 계속 늘어나는 문제가 발생.  
해결해야 한다.
![]({{ site.url }}/assets/jekyll/2019-02-06-jekyll_pagination/4.png)
