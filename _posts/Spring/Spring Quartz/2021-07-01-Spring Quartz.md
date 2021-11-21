---
title: "[개발자 블로그] Spring Quartz" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-07-01 +0800
categories: [Spring] # categories는 최대 2개까지 가능
tags: [spring, quartz] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Spring-Quartz
- 주기적으로 특정 작업을 반복하여 스케줄링을 하기 위해 사용한다.(매 1시간 마다 또는 정해진 시간에 특정 프로세스를 수행)

# Spring을 활용한 Quartz 사용법
## 1) Dependency추가
<img width="544" alt="스크린샷 2020-07-22 오후 5 49 54" src="https://user-images.githubusercontent.com/44339530/88155964-c2206700-cc43-11ea-9dd6-31c94d3e7fbb.png"><br>
<img width="653" alt="스크린샷 2020-07-22 오후 5 51 14" src="https://user-images.githubusercontent.com/44339530/88156124-f1cf6f00-cc43-11ea-9235-563dd08dc698.png"><br>

- <b>※ spring-context-support는 QuartzJobBean을 상속받기 위해 추가해줘야함</b>

## 2) Quartz설정 파일 추가(quartz-context.xml)
<img width="906" alt="스크린샷 2020-07-22 오후 5 53 27" src="https://user-images.githubusercontent.com/44339530/88156339-41ae3600-cc44-11ea-90cf-e3cda32eee6d.png"><br>

- `※ QuartzJobBean을 상속하여 DB와 관련된 작업을 처리 할 서비스빈을 의존성 주입하기 위해 JobDetailFactoryBean에 jobDataAsMap프로퍼티로 서비스빈으로 등록해줘야함`

## 3) Service Bean 및 QuartzJobBean을 상속한 클래스 생성 후 executeInternal메소드 오버라이딩
<img width="836" alt="스크린샷 2020-07-22 오후 5 57 32" src="https://user-images.githubusercontent.com/44339530/88156765-d31da800-cc44-11ea-9d4d-2d423c242bff.png"><br>
<img width="430" alt="스크린샷 2020-07-22 오후 5 58 34" src="https://user-images.githubusercontent.com/44339530/88156859-f7798480-cc44-11ea-9d75-7051317a73d5.png"><br>

## 4) 실행 결과
<img width="378" alt="스크린샷 2020-07-22 오후 6 01 06" src="https://user-images.githubusercontent.com/44339530/88157129-52ab7700-cc45-11ea-836d-600eca99d6d1.png"><br>