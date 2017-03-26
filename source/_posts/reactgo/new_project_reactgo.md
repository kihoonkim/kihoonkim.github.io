---
title: 새 프로젝트의 시작 React! Go!
date: 2017-03-26 14:56:00
tags:
  - 개발일지
  - reactjs
  - golang
categories:
  - ReactGo
  - intro
thumbnail:
---

오랜만에 웹 개발을 하게되었다. 2..3 년 만인가?  
내 웹 개발 경험의 대부분은 jQuery + Spring 였던 것 같다.  
최근에는 Angularjs는 ionic을 튜토리얼 수준으로 돌려보느라 사용해 봤고..([Ionic-Firebase](https://github.com/kihoonkim/ionic-firebase))  
실제 프로젝트에서는 Reactjs를 하루정도 사용해 본 것 같다.  

이제 안드로이드 개발 좀 할 만해졌는데.. 웹 개발이라니..  
몇 년 전만 해도 jQuery가 뭐든걸 다 해줄 것 같던 시대였는데.. 이제는 주변에서 아무도 jQuery 얘기를 하지 않는다.  
어찌됐든, 난 2017년이 되서야 [이 기분을](http://www.looah.com/article/view/2054) 느끼게 되었다.  

우리가 개발할 제품의 대부분의 기능은 화면에서 사용자와 인터렉션 하는 것이고  
백엔드와 통신보다는 View Component를 재활용 해서 구성하는 것이 많아 보였다.  
백엔드는 약간의 Rest API와 단순한 DB 처리 수준으로 예상 되었다.  
Angular2로 프로토타입 수준으로 개발이 되어 있긴했지만,  
팀원들 대부분 웹개발이 JSP나 jQuery 수준에 머물러 있었고,   
Angular2 뿐만 아니라 Typescript에 대한 런닝커브가 꽤 클 것 같았고,  
MVC framework보다는 단순히 View library만 있으면 될 것 같아 과감히 버리기로 결정했다.  

결국 ReactJS + SpringBoot 로 결정하고, 바로 기본적인 Test Code와 함께 CI를 구성했다.  
React를 열심히 굴려보고 있는데.. 우리의 진취적인 PM인 Keen의 한마디가 던져졌다.  
> 회사에서 개발하면서 이렇게 작은 크기의 백엔드를 할 일이 거의 없는데..
  **Golang** 으로 해보는게 어때요?

react-redux에서 맨붕 중이었는데.. Golang 이라니..  
시스템프로그래밍 할 일도 없고.. 동시성 처리할 일도 없을 것 같은데... Golang 이라니..  
하지만, 우리는 새로운 것에 목마른 순종자들이기 때문에.. SpringBoot 는 과감히 버렸다.  

Golang지농업.. 나시Golang 등의 아재 개그가 난무하며..  
이렇게 우리 개발팀 6명은 모든 익숙한 것을 버리고 새로운 환경에 던져졌다.  

개발 완료는 4월 말!!  
우리 개발팀의 Reactjs + Golang 삽질기를 4월 말까지 공유해 보고자 한다..
![](/images/react_logo.png) ![](/images/gopher.png)


잘... 되겠지??  
