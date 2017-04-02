---
title: Golang 뭐로 개발하지?
date: 2017-03-27 23:13:00
tags:
  - 개발일지
  - golang
categories:
  - ReactGo
  - golang
thumbnail:
---

개발을 시작 할 때 언제나 선택의 순간이 온다.  
언어, IDE, framework, Package Manager 등등..  
백엔드 언어는 Go 를 사용하기로 했기때문에 그에 맞는 녀석들을 선택해야 한다.  

이제 막 시작하는 단계이기 때문에 Go와 주변 생태계를 깊이 알지 못한다.  
어차피 모르는데.. 좋고 나쁘다는 판단 기준도 없었기 때문에  
대부분 단순히 끌리는 대로 선택하거나 그냥 제일 유명한 것들을 선택했다.  
당연히 잘못된 선택이 있을 수도 있지만, 그 또한 배움의 과정이라 생각했다.  
(사실 이름에 go 가 들어가는 녀석들 위주로 선택했다........)  

**Go 언어 문법** 은 [예제로 배우는 GO 프로그래밍](http://golang.site/)을 참조해서 공부했다.  
반나절 정도면 볼 수있고 설치 방법도 정리가 잘되어 있는 것 같다.  
C언어랑 javascript랑 짬뽕된 문법인 것 처럼 느껴진다.  
문법 자체는 심플하고 재밌는 요소들이 있는 것 같지만,  
아직 Go 언어가 가진 패러다임이 무엇인지 이해하지는 못했다..

**IDE** 는 Android Studio나 intellij를 주로 사용해 왔기 때문에 고민없이 **[Gogland](https://www.jetbrains.com/go/)** 를 선택했다.  
Gogland를 사용하면 좋은 점은 [이 곳](https://www.jetbrains.com/go/features/)을 보면 될 것 같다.  
![Gogland](/images/gogland.jpg)  

**Package Manager** 는 [Dep](https://github.com/golang/dep) 이 golang의 공식 매니져인 것 같지만, 아직 official tool 이 아니라고 나와있어서,  **[glide](https://github.com/Masterminds/glide)** 를 선택했다.  
(안드로이드 개발할 때 이미지 처리하느라 사용한 [glide](https://github.com/bumptech/glide) 랑 이름이 같아 친근한 나머지....)
![Glide](/images/glide.png)  

**Web framework** 은 [정말 많이 존재](https://github.com/avelino/awesome-go#web-frameworks)하는 것 같다.  
[Revel](https://revel.github.io/)이 제일 유명하고 많이 사용되는 것 같지만,  
우리는 Rest, websocket 정도만 지원되면 될 것 같아서 좀 더 가벼워 보이는 **[gorilla](http://www.gorillatoolkit.org/)** 를 선택했다.  
web framework 이라기 보다는 몇 개의 library를 모아놓은 toolkit 같아 보인다.  
mux, websocket 정도만 사용하지 않을까...  
![Gorilla](/images/gorilla.png)  

**ORM** 은 [GORM](http://jinzhu.me/gorm/)을 사용하기로 했다.  
모델이나 DB처리가 많거나 복잡할 것 같진 않지만..
특정 DB에 종속되거나 SQL 레벨로 작성하는 것을 좋아하지 않아서  
ORM기술을 자주 사용하는 편이다.  
GORM 정말 직관적인 이름인 것 같다. 발음은 고~옴 일까 고름일까..;;
현재 dialect로는 mysql, postgresql, sqlite, mssql만 지원하는 것 같다.  
spring-data-jpa 처럼 정말 편한 정도는 아니지만.. 단순 CRUD는  간단하게 사용할 수 있을 것 같다.  
안타깝게 로고는 없네....  

**TEST/Mock** 은 아직 고민 중이다.  
assertion을 위해서 [Gomega](http://onsi.github.io/gomega/)를 사용하고  
mocking을 위해서 [gomock](https://github.com/golang/mock)을 사용할까...  


제대로 비교해서 선택하려면 [awesome-go](https://github.com/avelino/awesome-go) 를 참조하면 될 것 같다.  

Golang 설치 부터 여러 패키지 설치 및 환경설정하며 겪은 삽질은 다음에 공유하기로.....  
