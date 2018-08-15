---
title: 마이크로서비스 아키텍처(MSA). 서비스 개발팀 이야기
date: 2018-03-25 15:44:40
tags:
 - microservices
 - msa
 - agile
categories: Microservices
thumbnail: /images/ms-article.png
---

2015년 쯤 아키텍처 연구팀에 잠깐 있었는데, auto Scale in/out, 도커, 대용량 데이터베이스, 대용량 스토리지 등이 연구되고 있었다. 내가 있던 셀은 SaaS나 멀티테넌트 아키텍처가 주된 관심사 였다. 이런 주제들에 적합한 아키텍쳐가 무엇인지 알아보다 보니 자연스레 MSA로 초점이 맞춰졌다. 그때 조대협 님을 초청해서 세미나도 듣고, [Chris Richardson](http://microservices.io/) 의 일주일짜리 강의도 듣을 수 있었다. 그 당시 MSA계의 국내외 최고의 거장이자 연예인이 아니었나 싶다.

![Chris Richardson](/images/chris.jpg "")

지금와서 생각해봤을때 개인적으로 가장 반성할 점은 MSA를 대용량 처리에만 초점을 맞추고 사내에 전파를 시도했던 것이다. 사실 SDS같이 B2B를 주업으로 하는 곳은 몇몇 분야를 제외하고는 대부분 대용량 트래픽이나 동시성 이슈가 거의 없다. 10K 문제 조차도 개발자 레벨에서 접해볼 기회가 없을 정도인데, MSA 를 그런 이유로 사용하자고 했으니, 관심을 가질 이유가 없었을 것이다. 모놀로틱하게 waterfall 방법으로 개발하고 기존 경험을 기반으로 운영해도 큰 어려움 없이 대부분의 문제를 해결 할 수 있었다. 

그러던 중 SI사업을 접고 대부분 솔루션 개발로 회사 비전이 바뀌면서 애자일에 대한 요구가 높아졌다. '우리가 이런 기능을 개발했으니 쓰세요' 에서 '어떤 기능을 어떻게 쓰고 싶으세요?' 로 변한 것이다. 개발하는 제품의 초점이 회사에서 사용자로 옮겨간 것이었다. 초기에 모든 요구사항을 수집하고 설계하고 개발하고 테스트하던 아주 긴 호흡의 개발 방법이 통하지 않는 시대가 온 것이었다. 실제 사용자의 목소리를 듣고 이를 반영해야 했다. 요구사항은 언제나 변할 수 있었다. 이런 방법을 적용하고자 Pivotal labs와 ThoughtWorks의 방식을 적절히 받아드린 ACT라는 곳이 회사에 생기게 되었다.

우리 팀은 개발 팀을 작게 구성하고, PM(product Manager), Dev, CX라는 역할자만 팀내에 두고 각자의 역할과 책임을 가지고 제품에 ownership을 가지고 빠르게 개발해 나가길 원했다. 하지만 SDS라는 대기업, 그안에 있는 사업부, 개발팀은 거대한 공룡과 같았다. 작게 개발하고 자주 배포할 수 있는 구조가 아니었다. 한 팀을 그렇게 만든다고 나머지 톱니바퀴가 같이 돌아갈 리가 없었다. 전체 팀이 Agile하게 일하고 독립적으로 빌드 배포가 가능해지길 원했다.

우리가 일하는 방식, 우리가 원하는 개발 문화를 가능하게 해줄 아키텍쳐가 필요해졌다. 그렇게 2년만에 우리는 다시 MSA를 꺼내 들게 되었다.

![The microservice architecture enables teams to be agile](/images/successtriangle.png "https://blog.eventuate.io")

> [eventuate blog](https://blog.eventuate.io/2017/01/04/the-microservice-architecture-is-a-means-to-an-end-enabling-continuous-deliverydeployment/)
The microservice architecture enables teams to be agile and autonomous. Together, the team of teams and the microservice architecture  enable continuous delivery/deployment.
마이크로 서비스 아키텍처를 통해 팀은 민첩하고 자율적으로 업무를 수행 할 수 있습니다. 모든 팀들이 마이크로 서비스 아키텍처를 함께 사용하면 지속적으로 전달 / 배포 할 수 있습니다.

서론이 좀 길었다. 그다지 경험도 없는 마이크로서비스 아키텍처를 그 복잡도를 감수하면서까지 맨땅에 해딩하듯이 도입하게된 배경을 설명하고 싶었다. MSA를 적용하고자 할때는 가장 근본적인 목적이 있어야 한다. MSA는 서비스들이 잘 동작하게 만들기 위해서 별도의 수많은 컴포넌트가 있어야 하고, 그를 위해 많은 의사결정 포인트가 존재 한다. 이때 근본적인 목적이 그 선택의 기준이 되어 준다.

예를 들면, MSA에서 가장 이슈가 되는 서비스의 크기는 얼마 만해야 하는가 이다. 이론적으로 서비스를 Bounded Context 단위로 나누고, Bounded Context는 하나의 Aggregate를 갖는 것이 좋다고 말한다. 그렇게 되면 서비스가 굉장히 잘게 나눠져야 하고, 한 팀이 여러 서비스를 관리해야만 한다. 즉, 코드 repository가 많이 지고 각각의 빌드 파이프라인도 많이 진다. 여러 서비스를 개발하기위해 IDE도 자주 스위칭을 해야 할 것이다. 우리는 크게 4,5 개 정도의 도메인을 식별했고, 그안에서 더 나눠져야 하는 서비스가 있다면, 정말 큰 이유가 있지 않다면 동일한 서비스내에 놓고 Rest URI 수준에서 분리했다. 우리의 근본적인 목적은 여러팀이 Agile하게 일할 수 있도록 만드는 것이 었기 때문이다.

프로젝트의 기본적인 기술스택은 다음과 같다.

서비스
- Java8
- Spring-boot-web
- Spring-data-jpa, QueryDSL, MaridDB, flyway
- Spring-cloud-stream, rabbitmq
- Swagger (spring-fox-swagger-ui)
- Test: junit, mockito, wiremock, rest-assurd, spring-cloud-contract

공통
- Spring-cloud : Zuul, Eureka, Config Server, Hystrix, OpenFeign(Ribbon), Sleuth
- Elasticsearch, Fluentd, Kibana
- Redis
- AWS

**서비스 레벨**는 각 서비스 개발팀 마다 조금씩 다르지만 거의 비슷하다. Database만 데이터의 목적에 맞게 다르게 가져간 정도이다.
**아키텍트 레벨**에서 가져가는 공통 부분은 처음부터 다 들어온 것은 아니고, 진행 중에 필요에 의해서 하나씩 추가되었다.

**테스트 전략**은 백엔드 서비스에서는 크게 4단계로 진행했다. (*각 테스트의 이름은 흔히 통용되는 것과 좀 다를 수 있다. 전체 팀이 모여서 각 테스트 단계의 목적과 의미에 대한 합의가 필요하다.*)(참조: [Testing Strategies in a Microservice Architecture](https://martinfowler.com/articles/microservice-testing/))

- **unit test**: 클래스 레벨, 주로 의존관계에 있는 클래스는 mocking.
- **functional test**: 서비스 레벨, 다른 서비스의 API or Message queue는 stubbing. 자신의 서비스 내에서는 end-to-end (api호출 ~ database까지)
- **contract test**: 서비스간 api 사용하는 spec만 테스트
- **integration test**: 실제 테스트 환경에서 실행. 전체 서비스와 database, rest api, message queue 등 모두 실제로 흐름

각 기술스택이나 테스트 단계에 대해서는 다음에 다시 정리하기로 하고, 이번 글의 본래 목적이었던 **MSA로 진행하면서 겪었던 문제들**에 대해서 되돌아 보고자 한다.

#### 개발팀과 운영할 팀이 다르다.
가장 안타까운 부분이다. 이 제품은 우리팀이 개발한 후에 다른 팀에 인수인계를 하는 것이 전제되어 있었다.  따라서 개발하는 팀의 이해관계자와 운영할 팀의 이해관계자가 다를 수 있고, 사용자도 다를 수 있다. 개발팀의 경험과 실력또한 다를 수 있다. 그렇기 때문에 Product Owner 입장에서는 모든 의사결정을 보수적으로 가져갈 수 밖에 없다. 아키텍트 역시 기술 스택은 java, spring을 기본으로 가져가기를 원했다. 심지어 controller-service-dao 패턴을 반드시 지키기를 원했다.
진정한 Decentralized 된 MSA를 적용하고자 한다면 'You build, You run it' 이 기본 전제가 되어야 할 것 같다.

#### 모든 부분에서 Polyglot 하게 할 수는 없다.
프로젝트 초반에 가장 큰 싸움이었던 것 같다. 파이썬을 좋아하는 개발자도 있고, nodejs를 좋아하는 개발자도 있다. 개인적으로는 Go로 개발하는 것에 재미를 들이고 있던 시기였다. 하지만 우리는 다뤄야할 도메인과 데이터에 관계없이 java, spring을 사용해야만 했다. msa는 목적에 맞게 폴리글랏 하게 개발 할 수있는 장점이 있다. 하지만, 일부 개발자가 좋아하는 언어로 개발해도 된다는 말은 아니다.
개발 언어가 다양해지면, 전체 시스템은 관리가 복잡해 진다. 빌드환경, 배포환경도 달라져야 한다. 테스트나 모니터링을 위한 툴도 다양해야 하고, 튜닝할 포인트도 각기 다를 것이다. 즉, 언어가 많아질 수록 전문성이 낮아 질 수 밖에 없다. 이러한 이유로 netflix도 jvm 언어로 시작한 것 같다.
(사실 java8 이후 부터는 java로 개발하는 것도 꽤 만족스럽긴 하다.)

#### Local Test가 힘들다.
모든 기능이 한 프로젝트에 담겨있고, 하나의 DB를 바라보고 있다면, local 환경에서 테스트하기가 쉽다. 특히 docker가 이를 더 쉽게 도와준다. db, rabbitmq 를 설치해서 실행하는데 몇 초면 끝난다. 하지만, MSA는 여러 서비스가 존재한다. 내가 호출해야하는 서비스가 다양한 경우 이를 모두 local 환경에 구축하긴 매우 어렵다. 다른 서비스의 내부 환경을 이해해야 한다는 뜻이기 때문이다.
주로 내 데이터는 로컬 환경, 다른 서비스는 테스트 환경을 바라보게 하는데, 해당 서비스가 내려가있거나, 가끔 데이터가 꼬이는 일이 발생하기도 한다. 다같이 데이터 한번 지웁시다! 하는 말도 안되는 상황이 발생하곤 했다.

#### Monolithic환경에서는 항상 살아있던 다른 서버스 들이 간혹 죽어있다.
MSA에서 서비스는 *다른 프로세스*에서 실행되는 컴포넌트라는 의미다. 즉, 다른 서비스가 살아있음을 보장 할 수 없다. 다른 서비스를 호출했는데 503 (Service unavailable) 상태코드가 리턴된 경우, 내 서비스에서는 어떻게 반응을 해야 할까 고민을 해야 한다. monolithic 환경에서는 할 필요가 없는 고민이었다. lifecycle이 항상 같았으니까..
MSA에서는 Circuit Breaker 패턴을 사용할 수 있다. 우리는 이를 위해 Hystrix를 적용했다. Hystrix는 죽어있는 서비스에 대해서 circuit을 open 해서 요청이 timeout 될 때까지 길게 기다리지 않게 하고, 비즈니스 요구사항에 맞게 fallback 정책을 가져갔다. 같이 503을 리턴하는 경우도 있고, 일시적으로 캐싱된 데이터를 리턴하는 경우도 있었다.
하지만, 인증관련 서비스가 내려가있다면...휴...

#### ACID가 보장이 안된다.
MSA는 기본적으로 분산환경이다. 모든 서비스가 네트워크 상에 존재한다. 따라서 데이터의 일관성을 보장 하기 어렵다. 전체 서비스가 엮이면 @Transactional 어노테이션의 마법이 통하지 않는다. 2PC는 어렵고, 해본적도 없다. 포기하는 편이 마음이 편하다. http로 요청을 보냈는데, 서비스가 죽어있다면 다른 서비스에 데이터 반영하는 것을 포기하고 내꺼만 잘 반영하자는 의미가 아니다. 우리는 이런 문제를 위해 다른 서비스에 CUD를 발생시켜야 하는 경우에 Event Driven Architecture를 적용하고 Eventual Consistency를 달성하고자 노력했다. Rabbitmq를 통해 message를 전달했다. rabbitmq가 qos level 1을 지원하므로 최소 한번은 전달하는 것을 보장해 준다.
하지만 message 전달이 실패하거나 서비스에서 처리 중 에 오류가 난 경우 보상작업을 해주거나 별도 비즈니스 흐름을 가져가야 하는 복잡함이 추가로 생겨났다.

#### 다른 서비스의 모델이 변경되는 것에 영향을 받는다.
내가 가진 데이터를 Rest API로 다른 서비스 혹은 UI에 제공한다는 의미는 API를 통해 다른 서비스들과 계약을 맺는 것으로 바라 볼 수 있다. 하지만 소프트웨어 개발이 언제나 그렇듯 요구사항은 변경된다. 즉 데이터가 변경되고 모델이 변경될 수 있다. 많이 하는 실수지만 데이터베이스의 모델이 그대로 Rest API의 response로 드러난다. 이 경우 서비스별로 독립적으로 빌드 배포가 가능하다는 이유로 계약을 어기고 변경 후 배포하게 된다. 해당 API를 호출하는 순간 내 서비스 기능과 상관없는 에러를 보게 될 것이다. 특히 개발 중에는 더 빈번히 발생한다. 이를 위해 우리는 **Consumer driven contract test**를 추가하게 되었다.

하지만 contract test는 정말 귀찮다. spring cloud contract의 경우 다른 서비스 소스에 내가 원하는 api의 response를 spec 으로 추가해야 하고, 그 서비스에서 spec을 기준으로 verify test를 진행하고, spec을 만족하는 stub server를 만들어 maven에 배포해야 하고, 내 서비스에서는 그 stub server를 maven에서 내려 받아서 contract test를 작성해야 한다. 말로 설명해도 굉장히 복잡하고 귀찮다. 서비스 팀간에 많은 의사소통이 필요하다.
![Consumer-Driven Contract Testing with Spring Cloud Contract](/images/cdc_spring_cloud_contract.png "https://specto.io/blog/2016/11/16/spring-cloud-contract/")

이런 문제가 있을때 관련해서 많이 언급되는 것이 [Tolerant Reader](https://martinfowler.com/bliki/TolerantReader.html) 패턴 인데, spring에서 서비스간 통신시 jackson을 사용하는 경우 할 수 있는 것이 별로 없다. (참고: [Robustness principle](https://dev.to/napicellatwit/robustness-principle-with-jackson-3h5))

하지만 중요한 것은, 내 서비스에서 API를 제공하면서 다른 서비스를 어떻게 바라보느냐 이다. DDD context map에서 말하는 customer-supplier 관계로 바라본다면 supplier가 주도하지만 결과적으로는 customer에서 원하는 결과를 제공해 줘야 한다. 하지만 다른 서비스를 단순히 준수자(Conformist)로 바라본다면 내가 변경하는 대로 알아서 반응하기를 기대하는 것이다.(예를 들면, naver open api 스팩이 변경되면 사용하는 쪽에서 알아서 변경 해야하는 준수자가 되는 것이다.) 사실 변경하는 입장에서 다른 서비스를 준수자로 바라보는 것이 편하긴 하다. 하지만 내 변경으로 인해 전체 시스템이 동작하지 않게 될 수도 있다.

우리 프로젝트에서는 모든 API에 대해서 복잡한 Contract Test를 작성해 나가기 보다는 꼭 필요한 것에 작성을 하고, 다른 것들이 변경되는 것에 대해서는  커뮤니케이션으로 빠르게 해결해 나가는 방향으로 정했었다. 서비스 팀이 많지 않고 가까이서 함께 일하기 때문에 가능한 일이었다.

#### UI와 Service의 모델이 다르다.
UX 디자이너 입장에서는 구현의 복잡함에 관계없이 사용자에게 가장 가치있고 편한 디자인을 해야했다. 서비스 개발자 입장에서는 의존관계를 잘 분리하고 최대한 단순한 모습으로 소스와 데이터를 관리 해야했다. 하지만 간혹 잘 나눠진 여러 서비스를 호출해서 하나의 응답으로 리턴해야 하는 경우가 있는데, 이 경우 두 가치가 대립하게 된다. 양쪽다 잘 지켜져야 하는 부분이기 때문에 PM이 중간에서 밸런스를 잘 맞춰가며 의사결정을 해야 할 것이다. 
개발팀에서 고민했던 것은 이런 서비스간 합성(aggregation)이 있을 경우, 혹은 모델을 변경(transformation)해야 하는 경우 중간 mediation layer를 가져가야 하는 가였다. 결론적으로는 서비스 합성은 필요한 서비스의 controller에서 처리했고, 모델 변경은 UI에서 컨버팅하는 로직을 넣는 것으로 개발되었다. 
많은 블로그에서 API Gateway의 기능 중 하나로 mediation기능(aggregation, transformation...)을 처리할 수 있는다고 나와 있다. 개인적으로는 게이트웨이에서는 단순히 인증이나 라우팅 정도만 처리하고 서비스에 특화된 기능 들은 서비스내부에서 처리하거나 mediation을 위한 서비스를 별도로 놓는 것이 좋다고 생각한다.

#### 클라우드 비용이 많이 든다.
서비스가 늘어가고, Database, Eureka, Config server 등 컴포넌트가 추가되고, 이 모든 것들이 이중화되고, 테스트 환경이 늘어가면서, AWS 비용이 수직상승하게 되었다. 대기업에서 한달에 몇 백만원 정도의 비용이 뭐 그리 대수냐 싶겠지만은 B2B 제품들은 사용자 수를 늘리고 동시접속 처리를 잘하는 것이 목적이 아니다. 따라서 인스턴스를 늘리는 것 처럼 개발을 위해 서버 운영비용에 크게 투자하는 것을 최소화 해야 한다. 쉽게 말해서 수지타산이 맞지 않는다.
AWS를 쓰던 환경에서 Heroku로 옮기거나, 로컬 서버로 내리는 등의 변경이 자주 발생했다. 개발 환경은 처음부터 내부 서버를 이용하는 것이 마음 편할 것 같다. 이런 일이 자주 일어나다 보니 배포 환경 설정을 위해 Ansible을 적용하고 있다고 들었다.

#### 빛은 생각보다 느리다.
AWS는 서울 리전을 사용하고 비용 문제로 DB는 Heroku를 사용한 적이 있는데, Heroku는 한국 리전이 없다. 몇 ms 로 처리되던 것들이 몇 초가 걸리는 진귀한 경험을 하게된다. 네트워크가 빛으로 통신한다고 빠르다고 생각하면 정말 큰 오산이다. 만약 서비스간 통신이 바다를 넘나드는 일이 있다면 빠른 인터넷 속도를 경험하며 자라온 한국 사람들에게 있을 수 없는 경험이 될 것이다.

#### 아키텍트팀의 설정변경으로 인해 서비스의 소스가 다시 빌드, 배포가 될 때가 있다.
나는 우리 서비스의 소스가 개발팀의 변경에 의해서만 재 배포가 되기를 원했다. 아키텍트 팀의 환경설정 변경으로 인해 자주 재빌드 되고 그 과정에서 빌드가 실패하고 이로 인해 실제 기능이 잠시동안 배포되지 못한 일이 있었다. Spring cloud Config server가 도입된 이후 설정 변경으로 빌드가 깨지는 문제는 많이 해결되긴 했지만 알 수없는 설정이 생기기 시작했다. 또한 서비스의 로직과 상관없이 오류가 발생하기도 했다.
Code ownership이 어디까지 가질 것이냐, Whole team을 어디까지 바라볼 것 인가의 문제였다. 기본적으로 코드의 변경은 서비스 개발 팀으로 한정 해야 한다고 생각한다. 빌드가 깨지고 버그가 생기면 조치해야 하는 팀은 서비스 팀이기 때문이다. 서비스팀 밖에서 적용하고자 하는 코드가 있다면 Merge request를 이용했으면 좋겠다. 좀 더 나아가서는 [Service Mesh](https://medium.com/microservices-in-practice/service-mesh-for-microservices-2953109a3c9a) 패턴을 사용하는 것도 한가지 방법일 것 같다.

#### 서비스 개발자는 아키텍처를 얼마나 이해해야 할까?
서비스 팀 개발자에게 MSA로 개발하면 뭐가 다른 가요? 라고 물어봤을때 "똑같아요" 라는 대답을 하는 것을 많이 봤다. 그저 다른 팀 서비스를 클래스 참조하거나 DB Table join 하던 것에서 API로 호출 하는 것이 다를 뿐이다.
![Deploying Microservices: Spring Cloud vs. Kubernetes](/images/spring-cloud-vs-kubernetes.png "https://dzone.com/articles/deploying-microservices-spring-cloud-vs-kubernetes")
서비스 팀 개발자가 Netflix oss나 Cloud native application, Twelve factor app 등을 모두 알아야 할까? 위 표와 같이 수많은 컴포넌트들을 알아야 할까? 당연히 알면 좋고 몰라도 크게 상관 없지만, 팀 내에 누군가는 알아야 하고 팀원들에게 설명해 줄 수 있어야 한다고 생각한다. 예를 들어 RestTemplete을 사용하다가 Ribon Client로 바꾸는 것으로 결정이 됐다면, 왜 그런 결정이 됐고, 실제 시스템이 어떻게 흘러가게 될지 예측을 할 수 있어야 할 것이다.
Microservices Architecture는 말 그대로 아키텍처이다. 언급되는 대부분의 컴포넌트 들이 서비스 외부의 것들이고, 아키텍트 팀에서 관리가 될 것이다.
서비스 개발팀은 이런 아키텍처에서 잘 돌아 갈 수 있도록 **stateless, loosely coupling, high cohesion** 한 서비스를 만드는 것이 가장 중요한 것 같다.

#### MSA에서 DDD는 필수일까?
MSA와 항상 같이 언급되는 것들이 있다. DDD, CQRS, Event-Driven, Event sourcing 등등..
객체지향을 잘 이해하면 java로 더 아름답게 개발할 수 있는 것처럼, DDD를 이해하면 MSA를 더 잘 개발할 수는 있을 것이다. 하지만 필수는 아니라고 생각한다. 이런 용어들에 겁먹고 MSA를 시도조차 못하지는 않았으면 좋겠다.
내 서비스에서 다루는 데이터를 API를 통해서 연결만 해 준다면, 내부 구현은 기존에 사용하던 방식 그대로 해도 된다. 다른 서비스와 의존관계를 더 잘 분리 할 수 있도록 도와주는 도구들일 뿐이고, 필요에 의해서만 도입하면 된다.
개인적으로는 MSA에서 서비스 개발시 가장 중요한 것은 **Metaphor** 라고 생각한다. 본래의 의미에 가장 적절한 이름을 짓는 것, 같은 이름이라도 각 서비스에 맞는 의미를 부여하는 것, 이 것이 PM, Designer, Dev가 팀으로서 함께 고민해야 하는 가장 중요한 부분이다. 


### 결론적으로..
참 복잡하고 어렵다. 알아야 할 것도 많다. 우리는 이런 복잡도를 감수하면서 까지 MSA를 적용할 가치가 있는 것일까? 사실 지금도 매일 많은 문제에 부딪히고 있다. 하지만, 이 모든 과정이 좋은 개발 문화를 만들어 가는 밑거름이 되리라고 믿는다. 개발자로서는 정말 재미있는 경험이다. 내가 알고있는 모든 지식을 쏟아 부울수 있는 기회였다. 회사내 많은 제품들이 지속적으로 개선 가능한 구조로 개발될 수 있었으면 좋겠다.


#### 고려되지 못한 것들..
auto scaling, data partioning, design for failure....
