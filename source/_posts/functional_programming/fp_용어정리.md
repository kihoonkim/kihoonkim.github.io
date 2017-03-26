---
title: FP 주요 용어
date: 2016-12-31 16:17:39
tags: FP
categories:
  - programming
  - FP
thumbnail: /images/fp.jpg
---
# Functional Programming

## State
OOP makes code understandable by encapsulating moving part  
FP makes code understandable by minimizing moving part.

FP is programming **without assignment statements**.

절차보다는 결과에 집중
  - 명령형 처리 : **상태** 를 변경하는 일련의 명령들로 구성된 프로그래밍 방식

상태관리를 개발자가 아닌 언어/런타임이 하도록 한다

## Pure Function
side effect가 없는 함수 -> 쓰레드에 안전, 병렬적 수행 가능

## First class Function
- 변수에 할당 가능
- 인자로 전달 가능
- 반환값으로 전달 가능
- 런다임 생성 가능
- 익명으로 생성 가능

## High order Function
함수를 다루는 함수.
- filter : Collection의 부분 집합 구할때
- map : Collection의 그자리의 값을 변형 할때
- fold / reduce : Collection의 요소들을 처리하여 다른 길이의 결과를 원할때

## Closure (enclosing context)
내부에 참조되는 모든 인수에 대한 암묵적인 바인딩을 지닌 함수  
즉, 자신이 참조하는 것들의 context를 포함  

## Memoization
반복해서 사용되는 연산 값을 함수 레벨에서 캐시하는 것  
캐싱가능하려면 pure function 이어야 됨  
trade-off : 성능 vs 메모리

## Lazy evaluation (strict vs nonstrict)
무한 컬렉션을 전달 할 수 있을까?  
연산을 미리 하는 것이 아니라, 필요한 시점에 연산  

## 커링(curring)
multiple agrument 함수를 single agrument 함수들의 chain으로 변경하는 것  
process(x, y, z) --> process(x)(y)(z)

## 부분 적용 (partial application)
몇몇 인수에 미리 값을 적용하고 나머지 인수만 받는 함수를 리턴  
process(1,2,z) --> prc(z)

## Recursion
상태관리를 런타임에게 양도

## 참조 투명성 (referentially transpprent)
표현식을 그 결과로 치환해도 프로그램에 아무 영향이 없다면 참조에 투명하다  

## 코드 재사용
100개의 함수를 하나의 자료구조에 적용하는 것이  
10개의 함수를 10개의 자료구조에 적용하는 것보다 낫다  

주요 자료구조(list, set, map)와 거기에 최적화된 연산을 선호

## 오류 처리
예외 발생은 side effect를 발생  
함수형 언어들은 주로 값을 처리하기 때문에 흐름을 막기보다는 오류를 나타내는 리턴 값에 반응  
