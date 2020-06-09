---
layout: post
title: "Post 작성방법"
author: youngjin park
date: 2019-07-21 19:00
tags: [Post, 내부]
---

CLOUDMATE TECH BLOG에 오신걸 환영합니다.

포스팅 방법 입니다.

## 1. 글 작성.

아래를 참고하여 문법에 맞게 글을 작성합니다.
> - [Markdown 문법 정리](https://post.naver.com/viewer/postView.nhn?volumeNo=24627214&memberNo=42458017)
> - [Mastering markdown](https://guides.github.com/features/mastering-markdown/)
> - 업로드 되어있는 글(.md) 참조

### 사진 업로드
1. 사진같은 경우 repo의 [/files/](https://github.com/mate365/mate365.github.io/tree/master/files) 에 업로드 후 사용 할 수있습니다, 업로드 위치를 지정한 후
 `![이미지이름](/files/image.png)`  
 로 사용 가능합니다.
     
2. `![이미지이름](https://azureblob.blob.core.windows.net/img/blog/image.jpg)` 와같이 외부 링크를 걸어주셔도 됩니다.

Tip. github의 issue 기능에 붙여넣기했을때의 링크를 사용할 수 있습니다.

### 업로드 전 양식 입력
 글작성 마친 후 다음같은 작업을 합니다.
> 1. `yyyy-mm-dd-{simple_post_name}.md` 형식으로 파일이름을 변경.  
```
2019-07-21-CreatePost.md
```
> 2. 글 최상단에 아래와 같이 [front matter] 작성
```
---
layout: post
title: "Post 작성방법"
author: youngjin park
date: 2019-07-21 19:00
tags: [Post, 작성법]
---
```
> 3. 처음 글을 쓰는 필자라면 **글쓴이(author) 등록** (아래 내용 참고)

> 4. 위 front matter에서 `tags`를 통해 포스트가 분류. `_tags` 디렉토리 확인 후 등록 (아래 내용 참고)


## 2. 글 업로드
git 저장소 - [repository](https://github.com/mate365/mate365.github.io)
  1. 작업할 로컬 디렉토리에서 `git clone https://github.com/mate365/mate365.github.io.git`
  2. `git pull`을 통해 로컬 Repo의 최신화
  3.  작성한 글을 `_post` 디렉토리에 업로드 (+ 사진 업로드)

이후 아래의 두가지 방법중 **한가지 방법**으로 업로드
- 로컬 작업 (Git 명령어를 통한 업로드 방식)
  1. `git add $글위치 $사진위치`
  2. `git commit` 후 vim을 통해 커밋메시지 입력 OR `git commit -m "커밋메세지"
  3. `git push`

- Visual Studio Code에서 Git 업로드

![VScode에서업로드방법](/files/blog/postvscode.png)
  1. 왼쪽 메뉴의 git control 이동
  2. 업로드할 파일 staging
  3. Commit 메세지 입력
  4. Commit
  5. Push를 눌러 Publish 완료

**약 30초 후 포스팅 확인!**


## 3. 필자 등록 (최초 등록시 필요)
1. `_authors` 디렉토리에 name.md 이름으로 필자 정보 파일 추가
2. 업로드 되어있는 파일 참고하면서 작성
3. 이미지 파일 업로드 (/files/authors/name.png)

## 4. 태그 등록 (최초 등록시 필요)
1. `_tags` 디렉토리에 {tag이름}.md 로 파일 작성.
2. 업로드 되어있는 파일 참고하면서 작성
3. 파일이름과, 내부 name항목과 같아야함.




