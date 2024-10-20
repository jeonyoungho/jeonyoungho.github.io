---
title: "[Database] MySQL VARCHAR vs TEXT" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2024-09-23 +0800
categories: [Database] # categories는 최대 2개까지 가능
tags: [database, mysql, mariadb] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

글자수 제한이 큰 데이터를 저장해야하는 요구사항에 대해 데이터 모델링 작업을 진행하게 되었다.

그러면서 mysql 의 varchar 타입과 text 타입의 차이점에 대한 차이점에 대해 정리하게 되었다.

먼저 결론은 다음과 같다. 물론 DBMS 서버스펙이나 데이터 모델 그리고 유입되는 트래픽에 따라서 미치는 영향도가 다를수 있다는점을 감안해야 한다.

**VARCHAR**
- 최대 길이가 (상대적으로) 크지 않은 경우
- 테이블 데이터를 읽을 때 항상 해당 컬럼이 필요한 경우
- DBMS 서버의 메모리가 (상대적으로) 충분한 경우

**TEXT**
- 최대 길이가 (상대적으로) 큰 경우
- 테이블에 길이가 긴 문자열 타입 컬럼이 많이 필요한 경우
- 테이블 데이터를 읽을 때 해당 컬럼이 자주 필요치 않은 경우

### 1. VARCHAR와 TEXT의 저장 용량

#### VARCHAR의 저장 용량
VARCHAR의 테이블의 최대 길이는 65535바이트이다. 다만 charset 에 따라 글자수 제한은 달라진다.

- euckr: 글자당 2바이트, 65535 / 2 = 약 32767 글자까지 가능
- utf8mb4: 글자당 4바이트, 65535 / 4 = 약 16383 글자까지 가능

그리고 '(현재 저장된 byte의 크기) + (길이를 표현하는 byte)'로 구성된다.

예를 들어 'apple'의 경우, 문자열 'apple'은 5byte이며, 그 길이를 표현하는 바이트는 1byte이므로 총 6byte가 필요합니다. 여기서 주목할 점은 문자열의 크기에 따라 길이를 표현하는 접두사의 바이트 크기가 달라진다는 것입니다.

- 255byte 이하의 데이터 저장: 길이 접두사로 1byte를 사용, 실제 문자열 데이터는 최대 65,534byte를 사용
- 255byte를 초과하는 데이터 저장: 길이 접두사로 2byte를 사용, 실제 문자열 데이터는 최대 65,532byte 사용

65535바이트가 컬럼이 아닌 테이블 기준인점을 주목해보자.

```sql
mysql> CREATE TABLE tb_long_varchar (id INT PRIMARY KEY, fd1 VARCHAR(1000000));
ERROR 1074 (42000): Column length too big for column 'fd1' (max = 16383); use BLOB or TEXT instead

mysql> CREATE TABLE tb_long_varchar (id INT PRIMARY KEY, fd1 VARCHAR(16383));
ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs

mysql> CREATE TABLE tb_long_varchar (id INT PRIMARY KEY, fd VARCHAR(16382));
Query OK, 0 rows affected (0.19 sec)

mysql> ALTER TABLE tb_long_varchar ADD fd2 VARCHAR(10);
ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
```

위 sql 의 4번째 실행문을 살펴보면 오류가 발생한다. (에러 메시지에서도 잘 설명하고 있듯이) 이미 tb_long_varchar 테이블은 하나의 레코드가 저장할 수 있는 최대 길이가 65,535 바이트를 초과했기 때문에 더이상 새로운 컬럼을 추가할 수 없다는것이다.

하나의 VARCHAR 컬럼이 너무 큰 길이를 사용하면, 다른 컬럼들이 사용할 수 있는 최대 공간의 크기가 영향을 받게 된다. 그러다보니 MySQL 서버에서는 레코드 사이즈 한계로 인해 VARCHAR 타입의 최대 저장 길이 설정시에 공간을 아껴서 설정해야 한다.

#### TEXT 의 저장 용량
TEXT도 최대 65,535 바이트까지 저장할 수 있다.

하지만 VARCHAR와 달리 무조건 길이 접두사로 2바이트가 사용된다. 따라서 실제 문자열 데이터는 최대 65,533 바이트를 사용할 수 있다.

### 2. VARCHAR와 TEXT의 저장 방식 차이
RDBMS 에선 VARCHAR 이든 TEXT 든 기준 용량을 넘어간 대용량 데이터 저장시 `Off-Page` 라고 하는 외부 공간에 저장하고 그렇지 않을 경우 일반적인 B-Tree (Clustering Index)에 저장(이를 Inline 저장이라고 함)한다.

```sql
CREATE TABLE tb_lob (
  id INT PRIMARY KEY,
  fd TEXT
);

INSERT INTO tb_lob VALUES (1, REPEAT('A',8100));  -- // Inline 저장소
INSERT INTO tb_lob VALUES (2, REPEAT('A',8101));  -- // Off-Page 저장소

CREATE TABLE tb_varchar (
  id INT PRIMARY KEY,
  fd VARCHAR
);

INSERT INTO tb_varchar VALUES (1, REPEAT('A',8100)); -- // Inline 저장소
INSERT INTO tb_varchar VALUES (2, REPEAT('A',8101)); -- // Off-Page 저장소
```

예를 들어 위 sql 에서 8100글자(8100바이트)를 저장시엔 B-Tree에 Inline 으로 저장하고, 8101글자(8101바이트)를 저장시엔 Off-Page로 저장하게 된다.

> **note**: 이는 MySQL 서버의 레코드 포맷에 따라서 조금씩 다르게 작동하는데, 이 예제는 innodb_default_row_format=DYNAMIC 설정을 기준으로 테스트해본 결과이다. MySQL 서버의 InnoDB row_format에 따른 Off-Page 저장 방식 차이는 [MySQL 서버 메뉴얼](https://dev.mysql.com/doc/refman/8.0/en/innodb-row-format.html)을 참고바란다.

MySQL 서버의 레코드 크기 제한은 65,535 바이트이지만, InnoDB 스토리지 엔진의 레코드 크기 제한은 페이지(블록)의 크기에 따라서 달라지는데, 대부분 페이지 크기의 절반이 InnoDB 스토리지 엔진의 최대 레코드 크기 제한으로 작동한다.

**InnoDB 스토리지 엔진은 레코드의 전체 크기가 이 제한 사항(16KB 페이지에서는 8,117 바이트)을 초과하면 길이가 긴 컬럼을 선택해서 Off-Page로 저장하게 되는데, 이 예제의 두번째 레코드(id=2)의 fd 컬럼 값이 커서 이 컬럼을 Off-Page로 저장한 것이다.**

### 3. VARCHAR와 TEXT의 인덱스 적용 가능 여부
`VARCHAR` 타입은 인덱스를 생성할 수 있는 반면 `LOB` 타입은 인덱스 생성을 할 수 없다는 이야기를 하는 사람도 있지만, 사실은 둘다 최대 크기 길이 제한만 충족시켜 주면 인덱스를 생성 할 수 있다.

B-Tree 인덱스뿐만 아니라 전문 검색 인덱스도 TEXT 타입과 VARCHAR 타입 컬럼 모두 동일하게 생성할 수 있다.

### 4. VARCHAR와 TEXT의 메모리 활용
두 타입의 가장 큰 차이는 VARCHAR 는 메모리에 미리 할당함으로써 성능상의 이점을 챙길수있고, TEXT의 경우는 그렇지 않기에 매번 IO 작업을 할 때마다 필요한 만큼 메모리가 할당되게 된다.

MySQL 엔진과 InnoDB 스토리지 엔진은 uchar* records[2] 메모리 포인터를 이용해서 레코드 데이터를 주고 받는다. 이때 records[2] 메모리 객체는 실제 레코드의 데이터 크기에 관계 없이 최대 크기로 메모리를 할당해둔다. 여기서 VARCHAR는 최대 크기가 설정되어 있기에 미리 할당받아둘수 있지만, TEXT 나 BLOB은 그렇게할 경우 메모리 낭비가 너무 심해지는 문제로 인해 IO 작업이 필요할때마다 메모리를 새로 할당받도록 동작한다.

**note**: records[2] 메모리 공간은 TABLE 구조체(struct) 내에 정의되어 있으며 TABLE 구조체는 MySQL 서버 내부에 캐싱되어서 여러 컨넥션에서 공유해서 사용될 수 있도록 구현되어 있다.

**여기서 LOB 컬럼의 값을 읽기 위해서 할당 및 해제하는 메모리 공간은 Performance_schema에 의해서 측정되지 않는다. (MySQL 8.0.33 기준). 그래서 LOB용 메모리 할당 해제가 실행되는지 알 수 없어서 성능 영향도를 파악하기가 어렵다. 한가지 더 주의해야 할 것은 VARCHAR 타입에 저장된 값의 길이가 길어서 Off-Page로 저장된 경우, MySQL 서버는 TABLE 객체의 records[2] 버퍼를 사용하지 못하고 새롭게 메모리 공간을 할당해서 사용한다. 그래서 VARCHAR 타입에 매우 큰 값이 빈번하게 저장되는 경우는 주의가 필요하다.**

더 자세한 내용은 [당근 마켓 기술 블로그 포스팅](https://medium.com/daangn/varchar-vs-text-230a718a22a1)을 참고하면 좋다.



#### 출처
- [https://medium.com/daangn/varchar-vs-text-230a718a22a1](https://medium.com/daangn/varchar-vs-text-230a718a22a1)
- [https://dkswnkk.tistory.com/714](https://dkswnkk.tistory.com/714)
- [https://yscho03.tistory.com/291](https://yscho03.tistory.com/291)