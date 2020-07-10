---
layout: page
title: 블로그 작성법
permalink: /howtopost/
sitemap: yes
tags: [post]
---

CLOUDMATE TECH BLOG에 오신걸 환영합니다.

포스팅 방법 입니다.

처음 포스팅하시는 분은 아래 방법으로 업로드 하시면됩니다.

> 우선 Markdown 문법에 맞게 글을 작성하시는 것을 권장드립니다.

# 1. 필자 등록
필자등록에는 3가지가 필요합니다.

- (요청) Cloud팀으로 자신의 Github 계정정보를 전달합니다. (초대메일&인증)
  - 로컬 컴퓨터에 `git clone https://github.com/mate365/mate365.github.io.git`를 통해 repository를 다운받습니다.
  - IDE(VScode 등)을 통해 해당 프로젝트를 불러옵니다.
- 잘나온 프로필 사진을 준비합니다. (사원증 사진 권장)
  - [/files/authors](https://github.com/mate365/mate365.github.io/tree/master/files/authors) 위치에 업로드합니다.
- 필자등록을 위해 [/_authors](https://github.com/mate365/mate365.github.io/tree/master/_authors) 위치에 아무 파일을 참고해서 자기 정보에 맞게 입력합니다.
  - 예시)  파일명: youngjin park.md

```
---
name: youngjin park
title: 박영진
image: /files/authors/박영진.jpg
---

```

# 2. 태그 등록
Tech Blog는 태그 기반으로 검색이 가능합니다. 큰범위~작은범위까지 카테고리를 정해서 입력하시면 됩니다. **태그는 포스트작성시에 등록합니다.**

- [/_tags](https://github.com/mate365/mate365.github.io/tree/master/_tags) 위치에 업로드되어있는 파일을 참고하여 업로드를 해야합니다.
  - `파일명`과 `포스트의 태그`와 일치해야합니다.
> - 태그는 아래와같이 포스트 하단에 보여집니다.
> - 태그 기반으로 검색됩니다.  
> ![](2020-07-10-16-42-21.png)

# 3. 포스트 작성 및 업로드
포스트 작성과 사진 업로드 방법 입니다.
- Markdown 문법으로 포스트를 작성합니다. 
  > - [Markdown 문법 정리](https://post.naver.com/viewer/postView.nhn?volumeNo=24627214&memberNo=42458017)
  > - [Mastering markdown](https://guides.github.com/features/mastering-markdown/)
  > - 업로드 되어있는 글(.md) 참조

- 사진 파일 업로드
  -  [/files/blog/](https://github.com/mate365/mate365.github.io/tree/master/files/blog/) 위치 어디든 올립니다. (디렉토리 생성가능)
  -  포스트 사진위치에 `![이미지이름](/files/blog/image.png)` 으로 업로드 위치에 맞게 적습니다.

- 준비된 글의 최상단에 아래와같이 포스트 정보를 넣어줍니다.  
  > - layout: post
  > - title: "글 제목"
  > - author: 필자 이름
  > - date: yyyy-mm-dd hh:mm
  > - tags: [tag1, tag2, ...]

예시)
```
---
layout: post
title: "Post 작성방법"
author: youngjin park
date: 2019-07-21 19:00
tags: [Post, 내부]
---
```
- 작성된 포스트는 [/_posts](https://github.com/mate365/mate365.github.io/tree/master/_posts) 에 업로드 합니다.
  - 파일이름 : YYYY-MM-DD-title.md

# 4. Git upload
거의다 되어갑니다. 아래 두가지방법중 한가지를 통해 업로드합니다.

- VS code의 git plugin
  

---
ㅇㅇ
---
ㅇㅇ
---

그 다음 [repository](https://github.com/mate365/mate365.github.io)에 초대 후, 업로드 합니다.

아래 자세한 방법입니다.

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
git 저장소 - [repository](https://github.com/mate365/mate365.github.io) (사내 메신저를 통해 Github 계정 전달해주세요.)
  1. 작업할 로컬 디렉토리에서 `git clone https://github.com/mate365/mate365.github.io.git`
  2. `git pull`을 통해 로컬 Repo의 최신화
  3.  작성한 글을 `_post` 디렉토리에 업로드 (+ 사진 업로드)

이후 아래의 두가지 방법중 **한가지 방법**으로 Git을 통해 업로드
- **로컬 작업** (Git bash 명령어를 통한 업로드 방식)
  1. `git add $글위치 $사진위치`
  2. `git status`를 통해 업로드 할 파일이 stage에 올라와있는지 확인
  3. `git commit` 후 vim을 통해 커밋메시지 입력 OR `git commit -m "커밋메세지"
  4. `git push`

- **Visual Studio Code**에서 Git 업로드  

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






