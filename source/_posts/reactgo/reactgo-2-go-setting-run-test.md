---
title: Golang 설치부터 테스트까지 가보자
date: 2017-04-01 15:03:00
tags:
  - 개발일지
  - golang
categories:
  - ReactGo
  - golang
thumbnail:
---

설치관련해서는 정리가 잘된 곳이 많아서 넘어가려다가..  
개인 노트북(Windows10)에 다시 설치하면서 정리도 할겸.. 남겨 본다.  

### golang을 설치해 보자.  
https://golang.org/dl/ 에서 다운로드 받고 각 OS에 맞게 설치하면 된다.  
윈도우에서 설치할때는 C:\Go 에 설치되고, MAC에 설치할때는 /usr/local/go 에 기본으로 설치된다.  

### 개발할 작업디렉토리를 만들자
https://golang.org/doc/code.html#Workspaces
workspace를 원하는 곳에 원하는 이름으로 생성하자.  
java를 개발시 eclipse에서 workspace를 만들고 하위에 여러 프로젝트를 하는 개념으로 생각하면 안된다.   
한 프로젝트를 개발하기 위한 작업공간을 만든다고 생각해야 한다.  
```bash
mkdir %USERPROFILE%\goworkspace
cd %USERPROFILE%\goworkspace  
```
다음으로 workspace 아래에 세 디렉토리를 만들어 줘야 한다.  
**src** contains Go source files,  
**pkg** contains package objects, and  
**bin** contains executable commands.  
```bash
mkdir src pkg bin
```
```
C:\Users\kihoonkim\goworkspace>dir
2017-04-01  오후 03:22    <DIR>          .
2017-04-01  오후 03:22    <DIR>          ..
2017-04-01  오후 03:01    <DIR>          bin
2017-04-01  오후 03:01    <DIR>          pkg
2017-04-01  오후 03:01    <DIR>          src
```

### 환경변수 세팅
GOROOT, GOPATH를 환경변수에 등록해 주고, GOROOT/bin 을 PATH에 추가해 준다.  
![](/images/go_env.PNG)  
```
C:\Users\kihoonkim> go env
set GOARCH=amd64
set GOBIN=C:\Go\bin
set GOEXE=.exe
set GOHOSTARCH=amd64
set GOHOSTOS=windows
set GOOS=windows
set GOPATH=C:\Users\kihoonkim\goworkspace
set GORACE=
set GOROOT=C:\Go
set GOTOOLDIR=C:\Go\pkg\tool\windows_amd64
set GCCGO=gccgo
set CC=gcc
set GOGCCFLAGS=-m64 -mthreads -fmessage-length=0
set CXX=g++
set CGO_ENABLED=1
set PKG_CONFIG=pkg-config
set CGO_CFLAGS=-g -O2
set CGO_CPPFLAGS=
set CGO_CXXFLAGS=-g -O2
set CGO_FFLAGS=-g -O2
set CGO_LDFLAGS=-g -O2
```
gogland IDE를 사용한다면 GOROOT, GOPATH가 제대로 설정되었는지 확인하고, 세팅이 안되어 있다면 다시 지정해 준다.  
![](/images/gogland_env.PNG)  

### github에 만들어 놓은 repository를 연결 해보자
src\github.com\kihoonkim 까지 디렉토리를 만들고 git clone을 할 수도 있지만,  
실수를 줄이기 위해서 go get 을 통해 받아오고 git 설정을 하기로 했다.  
뭐든 정답은 없으니 편한 방법으로 하면 될것 같다.  
src, pkg, bin 이나 src 밑에 go get을 통해 다운받아지는 패키지들은 형상관리가 필요 없기 때문에..  
내 repository에 맞게 git init을 해줘야 한다.  
**git 주소 : https://github.com/kihoonkim/gogo.git**  
```
C:\Users\kihoonkim\goworkspace> go get github.com/kihoonkim/gogo

C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo> git init
```
![GoGland에서 git 설정](/images/gogland_git.PNG)

### Hello World 출력해 보기
go application을 실행 하기 위해서는 **main 패키지** 내에 **main 함수** 가 존재해야 한다.  
다음은 main.go 파일을 만들고 실행까지 시킨 화면이다.  
![](/images/gogland_helloworld.PNG)

### 테스트 코드 작성해 보기
https://golang.org/pkg/testing/   
테스트 코드 작성하는 것에 대해서는 다음에 자세하게 정리해 볼 예정이기 떄문에,  
일단 테스트 코드 생성 정도만 하고 넘어가보자.  
실제 실행되는 테스트는 없다.  
**go test** 를 통해 테스트 코드를 실행 시킬 수 있다.  
![](/images/gogland_test.PNG)  

### Commit && Push
jetbrains 에서 만든 IDE 들을 즐겨 사용하는 이유중 하나가  
git 연동하는 툴이 좋다는 것이다.  
커밋 전에 변경된 소스코드를 비교 해서 볼 수도 있고..  
패어프로그래밍을 할때 커밋전에 리뷰를 할 수도 있다.  
**git commit**  
![](/images/gogland_commit.PNG)  
**git push**
```
C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo>git push
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (5/5), 510 bytes | 0 bytes/s, done.
Total 5 (delta 0), reused 0 (delta 0)
To https://github.com/kihoonkim/gogo
 * [new branch]      master -> master
```

![Github에 푸쉬된 내용 확인](/images/github_gogo.PNG)  
