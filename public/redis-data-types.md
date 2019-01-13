---
title: "[TIL] Redis - Data Types"
date: 2018-09-26 21:46:28
tags:
  - TIL
  - Redis
---

* String
  * 1:1 관계
  * Key-Value 쌍
  * SET/GET

* List
  * 1:N 관계
  * 중복 허용
  * 큐/스택으로 활용
  * PUSH/POP
* Set
  * 1:N 관계
  * 중복 불허용
  * 집합 연산/카드 게임에 활용
  * 합집합/교집합/차집합 연산 가능
* Sorted Set
  * Score 있는 Set
  * Score 값으로 정렬
  * Score 값이 같으면 Value 값으로 정렬
* Hash
  * 테이블 개념
  * Hash Key - PK / Field - Column / Value - Value로 맵핑
  * Key가 PK와 같은 역할을 하기 때문에 Key 하나는 테이블의 한 행과 같은 셈