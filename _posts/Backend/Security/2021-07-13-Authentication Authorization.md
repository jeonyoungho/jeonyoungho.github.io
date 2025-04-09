---
title: "[보안] 인증과 인가"
date: 2021-07-13 +0800
categories: [Backend, Security]
tags: [authentication, authorization]
toc: true
comments: true
---

# 인증(Authentication)
<b>사용자가 누구인지 확인하는 것</b><br>

- ex) 건물 출입이 가능한 사람인지 확인
    - 삼성전자 직원입니까? -> 정직원 바로 출입 가능
    - 방문자 입니까? -> 정직원 아님. 출입증을 통해 인증해야함

# 인가(Authorization)
<b>사용자가 요청을 실행할 수 있는 권한 여부가 있는지 확인하는 것</b><br>

- ex) 출입할 수 잇는 건물 공간이 어디까지 인가?
    - 건물 관리자 입니까? -> 모든 공간에 출입 가능
    - 방문자 입니까? -> 허용된 공간만 입장 가능, 일부 공간 접근 불가 (관계자 외 출입 불가 지역 등)

#### 출처
- [https://ivorycode.tistory.com/entry/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization](https://ivorycode.tistory.com/entry/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization)