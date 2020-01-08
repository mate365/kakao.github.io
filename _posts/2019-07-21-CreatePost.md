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

### 사진 혹은 그림 업로드
1. 사진같은 경우 blog의 [Git repository](https://github.com/mate365/mate365.github.io)의
 [/files/](https://github.com/mate365/mate365.github.io/tree/master/files) 에 업로드 후,
 `![이미지이름](/files/image.png)`  
 을 사용하여 보여줄 수 있습니다.
     
2. `![이미지이름](https://azureblob.blob.core.windows.net/img/blog/image.jpg)` 와같이 외부 링크를 걸어주셔도 됩니다.

### 업로드 전 양식 입력
작성하신 글 `.md 파일` 맨 위에 다음같은 양식으로 업로드합니다.
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

예시
```
---
layout: post
title: "How to upload post"
author: youngjin park
date: 2019-07-21 19:00
tags: [jekyll]
---
```  

## 2. 글 업로드
git 저장소 - [Git repository](https://github.com/mate365/mate365.github.io)
1. 로컬에 clone합니다. (Git 명령어를 통한 업로드 방식)
    - Clone한 위치에서 terminal을 열고 `git pull`을 입력합니다. ( 로컬 Repo의 최신화를 위함 )
    - `_post` 디렉토리에 작성하신 글을 업로드합니다.
    - `git add $글위치`
    - `git commit` 후 vim을 통해 커밋메시지 입력 OR `git commit -m "커밋메세지"
    - `git push`
    - 끝

2. Github에서 `_post` 디렉토리에서 `Create new file` 을 통한 업로드 방식  
![Create new file](https://user-images.githubusercontent.com/16531527/71944036-ec5d8a80-3205-11ea-99fe-bd12b7dede9a.png)  
---
![image](https://user-images.githubusercontent.com/16531527/71944146-37779d80-3206-11ea-9035-b353ff14356f.png)
업로드하면 완료.


### 그래도 모르시겠다면, [참조](https://raw.githubusercontent.com/mate365/mate365.github.io/master/_posts/2019-11-28-Insight2019_1.md)
### 그래도 모르시겠다면 저를 불러주세요.
![Call Me](https://kr.seaicons.com/wp-content/uploads/2015/09/Giraffe-icon.png)

감사합니다.
