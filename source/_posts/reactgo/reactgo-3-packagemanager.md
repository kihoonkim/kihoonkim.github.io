---
title: golang Package Manager로 dependency 관리하기
date: 2017-04-02 13:18:00
tags:
  - 개발일지
  - golang
categories:
  - ReactGo
  - golang
thumbnail:
---

프로젝트를 할때 혼자 개발하는 것이 아니라면, **dependency를 명확하게 관리** 하는 것이 중요하다.  
개발 환경을 세팅할 때 절대로 특정 모듈이나 라이브러리가 설치되어 있다고 가정해서는 안된다.  
버전관리도 힘들고 서로 다른 환경에서 개발하게 될 수도 있다.  
또한 이는 CI/CD를 위해서도 매우 중요하다.  

회사에서 프로젝트 할때는 [glide](https://glide.sh/)를 사용하는데..  
[dep](https://github.com/golang/dep)이 golang의 공식 PM으로 될 것 같아 이번에는 dep을 설치 및 삽질을 해 보았다.  
현재 기준(17.04.02)으로 아직 오피셜 하지는 않은 것 같다. 깃헙에 아래와 같이 나와 있다.  
`dep is NOT an official tool. Yet.`
`Note that the manifest and lock file formats are not finalized`

### DEP 설치 및 실행
`go get` 명령어를 통해 간단히 설치 할 수 있다.  
GOPATH에 따라서 자동으로 설치되는 위치가 정해지기 때문에 어디에서 명령어를 실행하든 상관없었다.  
```
> go get -u github.com/golang/dep/...
```
![](/images/godep_install.PNG)  

dep init 명령어를 통해 dep을 설정 할 수 있다.  
아무 곳에서나 하면 안되고, **내 repository 로 이동해서** 실행해야 한다.  
```
C:\Users\kihoonkim\goworkspace> cd src\github.com\kihoonkim\gogo

C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo> dep init
```
아래와 같이 `vendor` 디렉토리와 `lock.json`, `manifest.json` 파일이 생성되는 것을 볼 수 있다.  
![](/images/dep_init.PNG)  

`dep ensure` 를 통해 새로운 패키지를 설치, 업데이트를 할 수 있다.  
glide는 버전을 명시 하지 않으면 최신 버전을 자동으로 받아 오는 것 같았는데..  
dep는 `@^1.0` 처럼 버전을 정확히 명시해 줘야 했다.  
```
gogo > dep ensure github.com/jinzhu/gorm@^1.0
```
`manifest.json` 에 아래 처럼 등록 된 것을 확인 할 수 있다.
```json
{
    "dependencies": {
        "github.com/jinzhu/gorm": {
            "version": "1.0"
        }
    }
}
```
하지만.. `vendor` 디렉토리에 해당 패키지가 설치되지 않았다...  

`dep ensure -update` 를 해도 반응이 없다......;;;;;  

**정식 릴리즈 되면... 써야겠다......ㅠ**
**일단 glide를 설치해서 사용하자.**   

### Glide 설치 및 실행
glide 설치는 [몇 가지 방법](https://glide.readthedocs.io/en/latest/#installing-glide)이 있지만, linux/mac 에서는 아래 쉘을 실행시키는 것이 제일 쉽다.   
```
> curl https://glide.sh/get | sh
```
윈도우에서는 쉘스크립트 실행이 안되서 `go get`으로 받아왔다.  
```bash
C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo>  go get -u github.com/Masterminds/glide

C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo>  glide init
```

`glide init` 을 실행하면 dependency 명시를 위한 `glide.yaml` 파일이 생성된 것을 볼 수 있다.  
![](/images/glide_init.PNG)  

GORM 패키지를 glide를 통해 다시 받아보자  
```
gogo>  glide get -u github.com/jinzhu/gorm
```
`glide.yaml` 파일에도 잘 들어가있고, `vendor`디렉토리에 패키지가 잘 받아져 온 것을 확인 할 수 있다.  
![](/images/glide_get.PNG)  

`vendor` 디렉토리에 받은 패키지를 모두 삭제하고 `glide install` 명령을 통해 다시 받아보았다.  
```
C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo>  cd vendor

C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo\vendor>  rmdir /s github.com
```
```
C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo>  glide install
[INFO]  Downloading dependencies. Please wait...
[INFO]  --> Found desired version locally github.com/jinzhu/gorm 5174cc5c242a728b435ea2be8a2f7f998e15429b!
[INFO]  Setting references.
[INFO]  --> Setting version for github.com/jinzhu/gorm to 5174cc5c242a728b435ea2be8a2f7f998e15429b.
[INFO]  Exporting resolved dependencies...
[INFO]  --> Exporting github.com/jinzhu/gorm
[INFO]  Replacing existing vendor dependencies
```

#### Glide 명령어 정리
- get glide
`curl https://glide.sh/get | sh`
- initialization
`glide init`
- configuration
`vi glide.yaml`
- resolve the dependency
`glide update`
- reporoducible installations
 `glide install`
- add more dependencies
    `glide get github.com/foo/bar`  
    `glide get github.com/foo/bar#^1.2.3`

#### Glide 관련 파일, 디렉토리
- [vendor](https://glide.readthedocs.io/en/latest/vendor/)
- [glide.lock](https://glide.readthedocs.io/en/latest/glide.lock/)
- [glide.yaml](https://glide.readthedocs.io/en/latest/glide.yaml/)


### gitingore
vendor 디렉토리나 glide.lock 파일은 glide update/install 시 생성되기 때문에 형상관리 될 필요가 없다.  
`.gitingore` 에 추가해 놓자.  
![](/images/glide_gitignore.PNG)  



**결론, dep가 공식 릴리즈 되기전까지 glide를 쓰자..
사용이 편하고, go get <--> glide get 처럼 명령어도 비슷해서 더 직관적이고 쉬운 것 같다.**  
