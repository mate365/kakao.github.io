---
layout: post
title: "Visual Studio Online 리뷰 1"
author: youngjin park
date: 2020-04-22 17:00
tags: [Microsoft, Microsoft Azure, Cloud, Azure, Visual Studio, VScode, VSonline]
---

# Visual Studio Online 특징
- 대부분의 프로그래밍 언어를 지원하는 가상 환경을 제공하고,  
최소한의 IDE 셋팅으로 어디서든 작업 혹은 협업할 수있는 툴을 제공하고 있다.  
  
# Visual Studio Online 생성
- [https://online.visualstudio.com/login](https://online.visualstudio.com/login)
- 로그인 후 첫 화면  
![로그인 후 첫화면](/files/blog/first_main.PNG)  

- Plan, Environment 두가지 생성만으로 사용 할 수있다.  
Create new plan  
- 지정한 Azure Subscription에 리소스 생성한다.
- 해당 Plan에 대해 과금이 이루어진다.  
![Create new plan](/files/blog/NewPlan.PNG)  
Environments  
- 위에서 지정한 Plan에서 진행할 Project에 대한 remote server 생성.  
![Create new Environment](/files/blog/NewEnv.PNG)  
```
Environment Name : 프로젝트 환경 이름
Git Repository : 비워두거나, git 주소 입력
Instance Type : Standard (Linux): 4 cores, 8 GB RAM,
                Preminum (Linux): 8 cores, 16 GB RAM. 두가지가 있음.
```
- [가격플랜 보기](https://azure.microsoft.com/ko-kr/pricing/details/visual-studio-online/)
  

# visual studio Online IDE
- Environment 생성 후 화면
- 특징 : Visual Code와 인터페이스가 비슷함.  
![env생성 후 화면(실패)](/files/blog/webenv.PNG)  
- 첫 생성시 connection 오류가 뜰수있음. (Reload)
- 성공시 아래 사진과 같이 오류 메세지가 사라짐.
![env생성 후 화면(성공)](/files/blog/webenv_suc.PNG)  
- 왼쪽 메뉴의 기능 설명
```
Explorer : 프로젝트 탐색기
Search : 찾기기능
Source Control : 소스 컨트롤
Run : 소스코드 실행
Extensions : 확장기능
Remote Explorer : host 서버의 env 탐색기
Github Pull Requests : github의 풀리퀘스트기능
Live Share : 코드 협업 기능
```

# Hello Python 작성
- hello.py 파일 생성 시 자동으로 추천 메세지가 뜸.
![간단한 python 코드 실행](/files/blog/Runpython.PNG)  
- Extension에서 Python Extension 설치를 통해 사용.  
![python설치](/files/blog/extensionInstallpython.PNG)  
- 설치 후 Reload 필요.  
- 실행(F5)  
![python실행](/files/blog/RunpythonRes.PNG)  

# frontweb 작성
- 로컬에서 작업할때, frontweb의 경우 직접 localhost에 접속하면서 디버그를 한다.
- 그렇다면 vsOnline에서는 host에 어떻게 접근할까?  
```
좌측 메뉴 remote explorer > 좌측하단 Forwarded Ports 추가 
> 실행시킬 웹서버 포트 입력 > 만들어진 메뉴를 통해 host로 접속할수 있음.
```

![Forward port](/files/blog/forwardport.PNG)  

# Nodejs
- [(예제 소스)](https://github.com/mate365/AzureDevOpsProblemSolving/tree/master/src)
 [(참고페이지)](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)
 [(참고영상)](https://channel9.msdn.com/Series/Visual-Studio-Online-Monaco/Getting-started-with-nodejs)
- 서버 실행 명령어
```
npm install
npm start
```
- 실행화면  
![run화면](/files/blog/node_run.PNG)  
- Forwarded Ports 추가를 통해 웹서버 접속 확인.
# python Django  
- [sample project](https://github.com/mate365/python_web_example) 참고
- 버전 선택  
![python version select](/files/blog/python_venv_choose.PNG)  
- `Python: Select Interpreter` 을 통해 버전을 선택
- `pip install -r requirements.txt` 을 통해 패키지 설치
- `Run(F5)` 를 통해 django 서버 실행  
![django 실행화면](/files/blog/djangoRun.PNG)  

- Python Extension 설치후 Run(F5) 하게되면 위와 같은 화면이 뜬다. Django를 골라 run 할수있다.
- IDE 자체 Run 기능은 django runserver 를 지원하고있다.

# 중간 후기
- visual code를 사용하는것과 매우 유사. 강력한 Extension 기능을 그대로 사용할수있음.
- Local환경이아닌 VM환경에서 모든 작업이 돌아감.
- 따라서 Local 환경을 따로 구성하지않아도 되는 편함이 있음.
- vscode의 extension을 통해 web이 아닌 app에서 그대로 사용가능하다.
[(참고링크1)](https://evols-atirev.tistory.com/28)
[(참고링크2)](https://code.visualstudio.com/docs/remote/vsonline)