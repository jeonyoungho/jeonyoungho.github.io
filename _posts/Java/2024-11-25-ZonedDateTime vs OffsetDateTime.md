---
title: "[Java] ZonedDateTime vs OffsetDateTime"
date: 2024-11-25 +0800
categories: [Java]
tags: [java]
toc: true
comments: true
---

글로벌 제품 개발시엔 아래와 같은 처리가 적합하다.

### 서버 측 처리

#### 데이터베이스 저장
- 모든 시간 데이터는 UTC로 저장
- 일관성 있는 데이터 관리와 시간 연산이 용이

#### API 통신
- 클라이언트와의 모든 통신은 UTC 기준 처리
- 서버는 클라이언트로부터 받은 시간을 UTC로 변환하여 처리

### 클라이언트 측 처리
- 클라이언트는 서버로부터 받은 UTC 시간을 사용자의 로컬 타임존으로 변환하여 표시

이때 서버 내부적으론 `ZonedDateTime` 보단 `OffsetDateTime` 을 활용하는게 더 적합하다.

`ZonedDateTime` 은 [DST](https://lifetraveler.tistory.com/554) 가 들어가있다보니 정치적 결정이나 DST 규칙 변경으로 인해 저장된 값의 의미가 달라질수있다.

OffsetDateTime 은 항상 동일한 시점을 나타내기에 일관성 있는 시간관리 방식을 적용시킬수있다. (정치적 결정에 상관없이..)

### Reference
- [https://jodmsoluth.tistory.com/5](https://jodmsoluth.tistory.com/5)
- [https://sujl95.tistory.com/86](https://sujl95.tistory.com/86)