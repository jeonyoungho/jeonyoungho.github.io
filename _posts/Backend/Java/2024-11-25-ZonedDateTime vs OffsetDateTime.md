---
title: "[Java] ZonedDateTime vs OffsetDateTime"
date: 2024-11-25 +0800
categories: [Backend, Java]
tags: [java]
toc: true
comments: true
---

![Image](https://github.com/user-attachments/assets/f4370663-b359-4d3e-a232-5c086ae166b9)
_출처: https://jodmsoluth.tistory.com/5_

# OffsetDateTime
- LocalDateTime(날짜 + 시간) + offset(UTC와의 차이) 을 포함한다.
- Instant와 같이 나노초 정밀도로 타임라인에 순간을 저장한다.

<img width="1084" alt="Image" src="https://github.com/user-attachments/assets/0cd02e53-a33b-46cf-a96a-926cbbe70b5f" />

- ZoneId의 구현체인 ZoneOffset으로 offset(UTC와의 차이)을 나타낸다.

# ZonedDateTime
- OffsetDateTime(날짜 + 시간 + offset) + timezone(지역) 을 포함한다.

<img width="1063" alt="Image" src="https://github.com/user-attachments/assets/e2f967ca-afcc-4e20-8808-76edf0201a94" />

- ZoneId의 구현체인 ZoneRegion으로 타임존을 나타낸다.
- [DST(Daylight Saving Time)](https://lifetraveler.tistory.com/554)와 같은 써머 타임의 정보(ZoneRules로 판단)가 포함되어 있다.

# 글로벌 제품 개발시 어떤 타입을 활용하면 좋을까?
서버단에서는 `ZonedDateTime` 보단 `OffsetDateTime` 을 활용하는게 더 적합하다.

`ZonedDateTime`은 DST가 들어가있다보니 정치적 결정이나 DST 규칙 변경으로 인해 저장된 값의 의미가 달라질수있다.

`OffsetDateTime`은 항상 동일한 시점을 나타내기에 일관성 있는 시간관리 방식을 적용시킬수있다. (정치적 결정에 상관없이..)

상세하게는 아래와 같이 처리하는걸 고려해볼 수 있다.

## 서버 측 처리 
- 모든 시간 데이터는 UTC 기준으로 데이터베이스에 저장(OffsetDateTime 활용) => 일관성 있는 데이터 관리와 시간 연산이 용이

### 클라이언트 측 처리
- 클라이언트는 서버로부터 받은 UTC 시간을 사용자의 로컬 타임존으로 변환하여 표시

### API 통신
- 클라이언트와의 모든 통신은 UTC 기준 처리
- 서버는 클라이언트로부터 받은 시간을 UTC로 변환하여 처리

### Reference
- [https://jodmsoluth.tistory.com/5](https://jodmsoluth.tistory.com/5)
- [https://sujl95.tistory.com/86](https://sujl95.tistory.com/86)
- [https://developer-pi.tistory.com/340](https://developer-pi.tistory.com/340)