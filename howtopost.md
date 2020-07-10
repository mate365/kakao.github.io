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
> ![](/files/blog/howtopost/2020-07-10-16-42-21.png)

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
  > 1. 왼쪽 메뉴의 git control 이동
  > 2. 업로드할 파일 staging
  > 3. Commit 메세지 입력
  > 4. Commit
  > 5. Push를 눌러 Publish 완료  
  > ![VScode에서업로드방법](/files/blog/postvscode.png) 

- Git bash를 이용한 업로드
  > 1. `git add $글위치 $사진위치`  
  ![](/files/blog/howtopost/2020-07-10-17-34-17.png)
  > 2. `git status`를 통해 업로드 할 파일이 stage에 올라와있는지 확인  
  ![](/files/blog/howtopost/2020-07-10-17-34-43.png)
  > 3. `git commit` 후 vim을 통해 커밋메시지 입력 OR `git commit -m "커밋메세지"  
  > 4. `git push`  
  ![](/files/blog/howtopost/2020-07-10-17-34-59.png)

# 5. 확인
30초 이내 [Tech blog](https://tech.cloudmt.co.kr/)에 포스트가 올라갑니다.
글위치나 간격, 사진위치가 안맞을수있으니 확인후 다시 commit, push 하시면 됩니다.

> 사내 Tech blog는 양식이 없습니다. 자유롭게 마음껏 올려주세요. 감사합니다.

---
Cloud Div. ***박 영 진***






