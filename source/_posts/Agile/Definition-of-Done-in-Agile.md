---
title: Definition of Done
date: 2017-01-27 16:16:39
tags:
  - agile
  - dod
categories: Agile
---
# Avoid Chaos in Agile Development by Defining "Done"
애자일 프로젝트는 유저스토리의 완료 처리를 통해 진행상황이 관리된다. <br>
따라서, 유저스토리의 **완료(Done)**의 기준에 대해 정확하고 일관성있게 이해하는 것이 중요하다. <br>

#### Key Challenges
- 소프트웨어 개발 프로젝트는 정확하고 투명한 진행상황 관리가 필요하다
- 애자일 프로젝트에서는 완전히 완료된 일만 트래킹한다
- 유저스토리에만 집중하다보면, 비기능 요구사항을 방치하게 될 수도 있다

#### Recommendation
- 유저스토리의 완료의 의미가 무엇인지 정의해라
- 유저스토리가 완전히 완료된 것만 트래킹해라
- 완료의 정의를 가능한 가볍게 유지해라


## Introduction
애자일 프로젝트는 프로젝트의 현재 상태를 정확히 알지 못해 실패하는 경우가 있다. <br>
누가 하고 있는지, 얼마나 많은 일이 남았는지 등이 명확하게 보여야 한다. <br>
간혹 요구사항이 누락되거나 stakeholder의 관심사항이 프로젝트 끝까지 남아있는 경우가 있다. <br>
이런 문제를 해결하려면, 완전히 완료된 유저스토리란 무엇인지, 그 정의를 모든 프로젝트 구성원이 이해하고 있어야한다. <br>


#### Is It Done Yet??
개발자가 **유저스토리를 완료(Done)**했다고 말했는데, <br>
Unit Test 코드가 없거나, VCS에 커밋하지 않았다면? <br>
QA가 해당 스토리는 테스트가 더 필요하다고 말한다면? <br>
나중에 개발자가 소스코드를 다시 수정했다면?? <br>
성능테스트를 해야되는 상황이라면?? <br>

스크럼마스터는 개발자에게 다음 스토리를 진행하도록 해야 할까?? <br>

각 개발자마다 다른 기준을 가지고 있거나, 전반적인 기준이 존재하지 않는 다면, <br>
그 프로젝트는 관리되지 않고, 혼돈 상태가 될 것이다. <br>


## Define What It Means for a Story to be "Done"
#### Why Define the Completion of a "Story"
**스토리** 레벨에서 **Done**을 정의하는 것은 프로젝트 진행상황을 정확하고 일관성있게 추적할 수 있게 한다. <br>
애자일 프로젝트에서는 단순히 **해당 이터레이션에서 계획된 스토리 대비 완료된 스토리의 수** 를 추적하면 된다.(번다운차트 활용) <br>
스토리가 완료되는 기준이 명확하지 않으면, 얼마나 일이 진행되고 있는지, 프로젝트의 현재상태는 어떤지 알 수가 없다. <br>

#### Define Completion of an "Iteration"
이터레이션 별로 테스트나 문서의 레벨이 다르다면, 이터레이션의 진행상황을 추적하기 어렵다. <br>
이터레이션이 지연되지 않게 하려면, 워크아이템을 제한하고 리스크를 낮출 필요가 있다. <br>
워크아이템을 이터레이션에서 개별 스토리로 이동시키고, 지속적으로 개선을 해라. <br>
리스크가 있거나 먼저 완료되야 하는 것이 있다면 우선순위를 조정하라. <br>

#### Define Completion of a "Project"
프로젝트의 진행상황을 어떻게 추적할 것인가? <br>
애자일 프로젝트 전체가 완료(Done)되는 것을 조직에서 어떻게 알 수 있는가? <br>

애자일 프로젝트는 프로젝트 기간동안 발견되는 새로운 정보(요구사항)를 받아들인다. <br>
이는 프로젝트 스코프가 계속 커지는 문제가 생길 수 있다. <br>
그렇기 때문에 애자일 프로젝트는 **기간과 자원등을 고정**할 필요가 있고, <br>
중요한 기능을 우선순위화 해서 먼저 완료될 수 있게 해야된다. <br>
그 기간이 완료됐다면, 제품은 납품(Delivered)되어야 한다. <br>


#### How to Define Done

*"It Compiled, so I'm Done." ???* <br>
**Define of Done** <br>
스토리가 완료되기 전에 해야될 일이나 만들어야 하는 것들이 무엇이 있는지 모두의 동의하에 명확히 해야된다. <br>
프로젝트에 영향을 주지 않는 범위에서 다양한 이해관계자의 요구를 반영하여 최대한 상세히 해야된다. <br>
이는 프로젝트 전체에서 잘 이해하고 있어야한다. <br>


## Do Not Track Partially Complete Work
*I am 82% done with that task.*  <br>
이 말은 스토리가 거의 완료(almost done)되었다는 의미로 사용했을 것이다. <br>
하지만, 아직 진행중인 스토리의 진행상황을 추적하지 마라. <br>
애자일 프로젝트에서 **스토리는 작다(small)**. 그러므로 단순하게 **Done / Not Done** 으로만 관리해라. <br>
거의 완료된 스토리를 완료된 것이라고 간주하다보면 배포하거나 데모할 수 있는 스토리가 없어질 수도 있다. <br>
**스토리가 고객에게 보여줄 준비가 되지 않았다면, 그건 Not Done 이다.** <br>

#### Apply Done at the Story Level
하나의 스토리의 모든 작업이 완료된 경우에 다음 스토리를 진행하라. <br>
일부 작업이 이터레이션이 끝날때 까지 남아있더라도, 완료로 간주해서는 안된다. <br>

#### Make Sure the Definition of Done Is Comprehensive
애자일은 문제의 가장 단순한 해결책을 찾는 것에 집중하기 때문에, 설계나 사용자, 기능 등에 관련된 문서를 없애려는 경향이 있다. <br>
완료(Done)의 의미를 정의할때, Architect, DA, UX, 보안담당자등의 의견이 반영되어야 한다. <br>

- Coding: 코딩 표준, 코드리뷰, 패어링 등의 수준이 모든 개발팀이 동의해야된다.
- Nonfunctional requirements: 소프트웨어는 기능이 잘 동작하는 것만으로 충분하지 않다. 다음과 같은 고려사항이 있다.
  * Security
  * Performance
  * Scalability
  * Production Readiness
  * Documentation
- Unit Tests: 코드 커버리지의 수준이 명확해야 한다.
- Functional, Integration and Acceptance Tests: 스토리 마다 이슈나 문제등이 진짜 해결됐는지 보장할 수 있어야 한다.
- Demo: 언제든지 새기능이 사용자에게 보여줄 수 있어야 된다.
- Application Life Cycle Management and Source Control: 스토리가 완료됐다면 VCS, ALM에도 정확히 반영해야 된다.
- Refactoring and Technical Debt reduction: 대부분의 코드들이 지속적으로 리팩토링되고 기술적 부채를 해결해 나가기 때문에 Done이 다시평가 되어야 한다.

## Keep the Definition of Done as Light as Possible
```
- Agile Manifesto 중..
"maximize the amount of work not done."
```
하지 않아도 되는 일을 최대로 찾아라. 즉, 일을 단순화해라.

광범위해게 완료(Done)을 정의하다보면, 정의가 너무 커지고 무거워지는 위험이 있다. <br>
미처 생각지 못했던 완료의 의미가 있다면 추가해나가고, 더이상 의미없는 항목은 빼려는 노력을 지속적으로 해야된다. <br>
Done의 정의가 너무 광범위하고 크면 애자일 개발팀은 느려지고 많은 장점을 잃는다. <br>

여러 산출물(Artifacts)에 대해서 효율성과 필요성에 대해서 지속적으로 평가를 해라.

#### Continuously Streamline
애자일 초기단계에는 Done의 정의에 새로운 항목이 추가되기 때문에 많은 시간이 필요하다. <br>
하지만, 이터레이션별 산출물을 간소화하고 최적화하다보면 시간이 줄어들 것이다. <br>
![fig](http://agilecoach.typepad.com/.a/6a00e54ee21bf28834013487f15110970c-pi "")

#### Simplify by Changing Artifacts
문서를 관리하는 이유는 크게 두 가지이다.
- 기존 개발팀이 아무도 존재하지 않은 상황에서 소프트웨어를 잘 유지보수 해야되는 경우 <br>
- 새로운 맴버가 들어온 경우 트레이닝 목적으로 사용하는 경우 <br>

애자일에서 문서는 프로세스로 대체할 수 있다. <br>
첫번째 경우는 코드를 명확하게 작성하고, 자동화 테스트를 함으로서 해결한다. <br>
두번째 경우를 위해서 패어 프로그래밍 기법을 사용하여 트레이닝할 수 있다. <br>

사용가능한 기술을 통해서 문서를 가볍고 효율적으로 바꿀수도 있다. <br>
문서를 Wiki로 바꾼다든가, Javadoc과 같은 툴을 사용할 수 도 있다. <br>


#### Simplify by Removing Artifacts
문서가 비즈니스 요구사항을 더이상 해결할 수 없거나, <br>
이를 생성하거나 유지보수하는 비용 대비해서 그다지 가치를 주지 못하는 것이라면 <br>
그 문서를 없애라. <br>

## Defining Done is Foundational to Agile Project Success
제대로 관리될때, 애자일 프로젝트는 폭포수 방식 보다 더 빠르게 가치있는 소프트웨어를 만들 수 있다. <br>
애자일 팀은 완료된 유저스토리를 통해서 진행상황을 추적하고 관리한다. <br>
그렇기 때문에 일관성있고 완벽한 완료(Done)의 의미가 중요하다. <br>

진행상황을 정확하게 측정할 수 있어야, 제한된 시간과 자원내에서 최대의 가치를 고객에게 전달 할 수 있다. <br>
