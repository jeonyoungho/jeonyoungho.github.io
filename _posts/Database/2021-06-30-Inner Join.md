---
title: "[개발자 블로그] Inner Join" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [Database] # categories는 최대 2개까지 가능
tags: [database, sql, innerjoin] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Inner Join
- Inner Join은 조인될 조건이 부합하는 행에 대해서만 Join이 발생하는 것(교집합의 영역)
- 대개의 업무에서 조인은 Inner Join을 주로 사용하며, 일반적으로 Join이라 하면, Inner Join을 지칭하는 것이다.

#### Inner Join 구조
~~~
SELECT <열 목록>
FROM <첫 번째 테이블>
    Inner Join <두 번째 테이블>
    ON <조인될 조건>
[WHERE 검색조건]
~~~

# Inner Join 예시
- 기본적인 이해를 돋기 위해 아래의 customer 테이블과 buy테이블 있다고 예시를 들어보자<br>
<img width="720" alt="1" src="https://user-images.githubusercontent.com/44339530/121804534-cf8e7780-cc81-11eb-8b7d-cd7ffb7a6246.png"><br>
<img width="721" alt="2" src="https://user-images.githubusercontent.com/44339530/121804537-d1f0d180-cc81-11eb-8672-10e3eb3a0072.png"><br>

- buy테이블을 보면, 물건을 구매한 사용자의 아이디(USER_ID)와 물건 등의 정보(PRODUCT_NAME, PRODUCT_PRICE)만 나타난다.
- 그런데 이 물건을 배송하기 위해서는 구매한 회원의 주소를 알아야한다.
- 이 회원의 주소를 알기 위해 주소 정보가 있는 user테이블과 결합하는 조인이 Inner Join이다.
- 참고로, 위 형식에서 INNER JOIN을 JOIN이라고만 해도 INNER JOIN이라고 인식한다.
- 이제 buy테이블에서 'buy3'이라는 아이디를 가진 사람이 구매한 물건을 배송하기 위해서, 두 개의 테이블을 통째로 조인하려면 아래와 같이 하면 된다.<br>

```
SELECT *
FROM "buy"                        -- 첫번째 테이블
        INNER JOIN "customer"          -- 두번째 테이블
        ON "buy".USER_ID = "customer".USER_ID -- JOIN 조건
WHERE "buy".BUY_ID = 'buy3'
```

- 위의 쿼리를 실행하면 결과는 아래의 사진과 같다<br>
<img width="1096" alt="스크린샷 2021-06-13 오후 8 04 11" src="https://user-images.githubusercontent.com/44339530/121804665-868af300-cc82-11eb-94ec-7db437dc6cc0.png"><br>

- <b>customer테이블과 buy테이블은 모두 USER_ID 컬럼이 존재하기에 반드시 테이블명.컬럼명 형태로 작성해야 한다.(별칭도 가능) 그렇지 않을 경우 오류가 발생한다.</b>

- 위의 결과를 생성하기 위해서 아래와 같은 과정을 거친다.
