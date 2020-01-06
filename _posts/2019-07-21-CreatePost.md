---
layout: post
title: "Post 작성방법"
author: youngjin park
date: 2019-07-21 19:00
tags: [Post, 작성법]
---

CLOUDMATE TECH BLOG에 오신걸 환영합니다.

글을 작성하는 방법을 알려드리겠습니다.

## 1. 글 작성.

우선 '[Markdown 문법 정리](https://post.naver.com/viewer/postView.nhn?volumeNo=24627214&memberNo=42458017)' 참고하여 문법에 맞게 글을 작성합니다.  
그리고 사진같은 경우 blog의 [Git repository](https://github.com/mate365/mate365.github.io)의
 [/files/](https://github.com/mate365/mate365.github.io/tree/master/files) 에 업로드 후,

`![이미지이름](/files/image.png)` 을 사용하여 보여줄 수 있습니다.
  
     
혹은 `![이미지이름](https://azureblob.blob.core.windows.net/img/blog/image.jpg)` 와같이 외부 링크를 걸어주셔도 됩니다.

## 2. 글 업로드
[Git repository](https://github.com/mate365/mate365.github.io) 를 로컬에 clone합니다.  
작성하신 글 .md 파일을 다음같은 양식으로 업로드합니다.
> 1. `_posts` 디렉토리에 `yyyy-mm-dd-slug.md` 파일로 복사(or 이동).
>  - slug: 해당 포스트의 고유 키로 url의 일부로 사용. 왠만하면 특수문자없이 영문자,숫자,-(하이픈),.(점)...만 사용.
>  - yyyy,mm,dd: 발행 년,월,일.
>  - 참고: 최종적으로 포스트의 url(permalink)는 http://tech.cloudmt.com/yyyy/mm/dd/slug/
> 2. 파일 상단에 [front matter] 작성
>  - `layout: post` # 레이아웃(필수). `page` 레이아웃을 사용하면 목록에 보이지 않는 글을 쓸 수 있음.
>  - `title: '제목'` # 제목(필수)
>  - `author: lastname.firstname` # 필자(필수). 왠만하면 회사 아이디(예: iolo.fitzowen) 사용
>  - `tags: [tag1,tag2,tag3,...]` # 태그 목록(선택). 왠만하면 특수문자없이 영소문자,숫자,-(하이픈),.(점)...만 사용.
>  - image: http://... # `커버이미지` url(선택)
>  - date: `YYYY-MM-DD HH:MM:SS` # 발행일(필수)
> 3. 처음 글을 쓰는 필자라면 **글쓴이(author) 등록**(필수)
> 4. 유력한(?) 태그가 새로 등장했다면 **태그 등록**(검색을 위한 도구)  
###예시

```
---
layout: post
title: "How to upload post"
author: youngjin park
date: 2019-07-21 19:00
tags: [jekyll]
---
```  

### 그래도 모르시겠다면, [참조](https://raw.githubusercontent.com/mate365/mate365.github.io/master/_posts/2019-11-28-Insight2019_1.md)
### 그래도 모르시겠다면 저를 불러주세요.
![Call Me](https://kr.seaicons.com/wp-content/uploads/2015/09/Giraffe-icon.png)

감사합니다.