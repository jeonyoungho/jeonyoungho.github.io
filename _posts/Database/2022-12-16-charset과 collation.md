---
title: "[Database] charset과 collation" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-12-16 +0800
categories: [Database] # categories는 최대 2개까지 가능
tags: [database, mysql, mariadb] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

MySQL 또는 MariaDB 를 사용하여 컬럼에 unqiue 제약 조건을 걸었더니 <b>대소문자를 구별하지 않는 것</b>을 새로 알게 되었고 그 이유에 대해서 알아보았다.

즉, `AAA` 와 `aaa` 와 동일하게 취급하여 유니크 제약 조건으로 인해 둘 중 하나의 insert가 안되는 것이다.

unquie 제약 조건을 추가하는 DDL 명령어는 아래와 같다.

```sql
ALTER TABLE `테이블명` ADD UNIQUE KEY 제약조건명 (`컬럼명`);
```

그리고 해당 테이블 DDL 컬럼에는 아래와 같이 명시가 되어 있었다.

```sql
create table (
`column_name` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
...
)
```

여기서 `COLLATE utf8mb4_unicode_ci` 가 의미하는 것에 대해 궁금증을 가지게 되었다.

### COLLATE
COLLATE 은 텍스트 데이터 정렬(ORDER BY)할 때 사용한다. 

즉, text 계열 자료형에서만 사용할 수 있는 속성이다.

주로 `uf8_bin(utf8mb4_bin)`, `utf8_general_ci (utf8mb4_general_ci)`, `utf8_unicode_ci(utf8mb4_unicode_ci)` 세 가지를 많이 사용한다.

#### uf8_bin
바이너리 저장 값 그대로 정렬하는 방식이다.

hex 코드(16진수)로 A는 41, B는 42, a는 61, b는 62이기 때문에

`A -> B -> a -> b` 의 순서로 정렬되어 출력된다.

### utf8_general_ci
<b>텍스트 정렬시 a 다음에 b 가 나타나야 한다는 일반적으로 널리 사용되는 정렬 방식이다.</b>

<b>MySQL은 기본적으로 ci(case insensitive) collation 을 사용하기 때문에, 알파벳의 대소문자를 동일 문자로 취급한다고 한다.</b>

라틴계열 문자를 사람의 인식에 맞게 정렬한다.

즉, `A -> a -> B - b` 의 순서로 정렬하게 된다.

### utf8mb4_unicode_ci
general_ci 보다 조금 더 사람에 맞게 정렬한다.

한국어, 영어, 중국어, 일본어 사용환경에선 general_ci와 unicode_ci의 결과가 동일하다.

뭔가 더 특수한 문자의 정렬 순서가 변경된다.

### MySQL 에서 권장하는 charset과 collation
1) MySQL 5.5.3 이전 = utf8 charset에, utf8_general_ci collation 사용

2) MySQL 이 최신 = utf8mb4 charset에, utf8_unicode_ci collation 사용

### utf8과 utf8mb4
charset utf6과 utf8mb4의 차이에 대해 궁금증을 가지게 되었다.

해당 내용은 아래 링크에서 너무 잘 설명해주고 있다.

[https://sshkim.tistory.com/128](https://sshkim.tistory.com/128)

간단하게 요약하면 아래와 같다.
UTF-8 문자 집합은 1~4 바이트까지 저장이 가능하게 설계되었다.(가변 바이트)
하지만 `MySQL`에선 `utf8`을 `3바이트 가변 자료형`으로 설계하였고, 최근에 나온 `4바이트 문자열(이모지 같은 것)`을 저장하면 값이 `손실`되는 문제가 있었는데

이를 보완하여 `가변 4바이트 UTF-8문자열`을 저장할 수 있는 자료형을 추가한게 `utf8mb4` charset이다.

![image](https://user-images.githubusercontent.com/44339530/208071758-037542b9-0d41-418b-89e4-bc44969fde94.png)

> **Note**: mariadb utf8 문자집합에서 영문은 1바이트, 한글은 3바이트, 이모지는 4바이트로 저장된다. ([참고](https://sleepyeyes.tistory.com/94))


### MySQL(MariaDB)의 대소문자 구별하는 유니크 제약 조건 추가하는 방법

collation을 `uf8_bin` 으로 설정하고 유니크 제약 조건을 추가하게 되면

정상적으로 대소문자를 구별하여 유니크 제약 조건이 걸리게 된다.

즉, a와 A가 둘 다 다르게 인식되는 것이다.

참고로 charset 변경 명령어는 아래와 같다.

```sql
-- sql
ALTER TABLE `테이블명` DEFAULT CHARACTER SET 캐릭터셋 COLLATE 콜레이션

-- 예시
ALTER TABLE `table1` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci
```

#### 출처
- [https://blog.naver.com/seuis398/220851196727](https://blog.naver.com/seuis398/220851196727)
- [https://sshkim.tistory.com/128](https://sshkim.tistory.com/128)
- [https://zetawiki.com/wiki/MySQL_%ED%85%8C%EC%9D%B4%EB%B8%94_collation_%EB%B3%80%EA%B2%BD](https://zetawiki.com/wiki/MySQL_%ED%85%8C%EC%9D%B4%EB%B8%94_collation_%EB%B3%80%EA%B2%BD)