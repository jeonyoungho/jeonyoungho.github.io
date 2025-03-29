---
title: "AWS ECS 기반 MSA 환경에서 서버리스 스케줄링 아키텍처 구축기"
date: 2023-10-01 +0800
categories: [Backend]
tags: [aws, ecs, msa, eventbridge, lambda, kafka]
toc: true
comments: true
---

# 배경
제품 비즈니스 요구사항중 휴직이 시작/종료된 구성원들에 대해 재직상태를 즉시 갱신시켜줘야 하는 요구사항이 도출되었다.(이전까지는 관리자가 수동으로 재직상태를 업데이트 해주는 방식이었음)

AWS ECS 기반 MSA 환경에서 첫 스케줄링이 필요한 요구사항이었고 이를 해결하고자 시작하게 되었다.

# 클라우드 아키텍처 설계
이전까지 사내 프로젝트들은 모놀리틱 구조로 스케줄링 요구사항은 단일 EC2 인스턴스에 스프링 애플리케이션을 배포하여 `@Scheduled` 어노테이션 기반으로 구현되었었다.

하지만 단일 EC2 인스턴스에 대한 인프라프로비저닝/CICD 구축 및 유지보수 비용 절감과 애플리케이션단의 개발 및 유지보수 하기 더 쉬운 구조를 잡기 위해 AWS 서버리스 아키텍처 기반 스케줄링 모듈을 설계하였다.

이전에 [인사 데이터 동기화 배치 시스템](https://jeonyoungho.github.io/posts/AWS-%EC%84%9C%EB%B2%84%EB%A6%AC%EC%8A%A4-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EA%B8%B0%EB%B0%98-2,000%EC%97%AC%EB%AA%85-%EA%B3%A0%EA%B0%9D%EC%82%AC-%EC%9D%B8%EC%82%AC%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%8F%99%EA%B8%B0%ED%99%94-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B5%AC%EC%B6%95%EA%B8%B0/)을 서버리스 아키텍처로 설계 및 구현했던 경험을 바탕으로 AWS EventBridge와 Lambda 기반으로 애플리케이션단에서 스케줄링이 가능한 구조를 설계하였다.

![Image](https://github.com/user-attachments/assets/9a4dc82a-9ec4-4c56-bd3b-26633549a25c)

상세한 구현 메커니즘은 다음과 같다.

**1) AWS EventBridge에 Rule 을 등록하여 json 페이로드를 람다에 인자로 전달하며 실행시키는 이벤트 발생**

```json
{
  "service": "cms",// microservice name
  "url": "/scheduling-api/xxx", // api path
  "method": "post", // http method
  "headers": { // http header
   ...
  },
  "body": { // http body
   ...
  }
}
```

**2) 발생되는 이벤트로 인해 호출되는 람다 함수(프록시 람다 함수)는 스케줄링을 실행시킬 마이크로서비스로 요청을 대신 전달해주는 프록시와 같은 역할을 수행한다. json 페이로드에 정의된 API 스펙 기반으로 요청을 대신 HTTP 프로토콜 기반으로 대신 전달해주게 된다.**
- AWS EventBridge에서 직접 마이크로서비스로 http 요청하는 방법도 가능하지만 스케줄링 실패에 대한 커스텀 핸들링(MsTeams 알림 발송 등)이 다소 어려운 한계가 있다보니 람다 함수가 해당 책임을 대신하게 되었다.

**3) 스케줄링 요청을 수신받은 마이크로서비스는 프록시 람다 함수로부터 들어온 요청을 처리하고, 아래 json 포맷을 기반으로 응답을 한다.**

```json
{
  "contents": {
    "failTenants": string[], // 실패 테넌트 목록
    "failCount": number, // 실패 테넌트 수
    "failDetails": [ // 실패 테넌트에 대한 상세 내용
      {
        "tenant": string,
        "stackTrace": string
        }
    ],
    "successTenants": string[] // 성공 테넌트 목록
  }
}
```

**4) 프록시 람다 함수는 응답 받은 json 데이터로부터 스케줄링 실행 결과를 팀즈 채널로 알림을 발송한다(webhook)**
- 추가적으로 실패한 테넌트가 하나 이상일 경우 MS Teams 의 '워크플로우'를 이용하여 단체 채팅방에 메시지를 발송한다.

# AWS 서버리스 아키텍처 도입을 통한 이점
해당 아키텍처 구조를 도입함으로써 얻을 수 있는 이점은 크게 다음과 같다.

- EC2 인스턴스 구동 비용 절감으로 인한 클라우드 비용 제로화
- 유지보수 효율성 극대화(인프라프로비저닝/CICD 구축 공수 및 유지보수 비용 절감)
- 이전 AWS EventBridge와 Lambda 기반 배치 시스템 구축 경험을 바탕으로 단기간내 효율적으로 구축 가능함
- 개발팀에서 핫픽스 대응시 스케줄링 api를 직접 호출해서 빠르게 복구 가능하다.
- 애플리케이션단에서 도메인 내부에 구현된 로직들을 재사용 가능하게 되고 응집도를 높일 수 있다.(별도 레포에 별도 모델을 정의하여 구현하였다면 스케줄링 레포를 확인해야만 재직 상태 갱신 요구사항을 알 수 있었을것이다)

# 도출된 문제 및 개선에 대한 고민
위 아키텍처로 운영하며 도출된 이슈는 다음과 같다.

다른 스케줄링 기능을 동료분께서 적용하였고, AWS ALB 유휴 제한 시간(당시 디폴트 1분)을 초과하여 Connection Timeout 에러가 발생하였다.

당시 선조치를 위해 DevOps 팀에서 ALB 유휴 제한 시간을 늘려 해결하였다.

하지만 스케줄링 요구사항으로 ALB 유휴 제한 시간을 늘린건 우회적인 조치이고 인프라 관점에서 좋지 못한 구조로 판단된다.

이를 개선하고자 한다면 HTTP 통신이 아닌 AWS MSK 메시지 기반으로 개선해볼 수 있을것 같다.

![Image](https://github.com/user-attachments/assets/d12d9808-c0ce-42fe-9c4c-32c20a1f9e41)

여기서 kafka 컨슈머 구현시 메시지 중복 소비 현상을 주의해야할 것이다.

auto commit 방식으로 운영하며 auto.commit.interval.ms 내에 로직 수행이 완료되지 못한채 리밸런싱이 발생하게 되면 메시지 중복 소비 현상이 발생하게 될 수 있을것이다.

따라서 컨슈머가 메시지를 한 개씩 polling 하여 수동 커밋 하는 형태로 구성해야 위와 같은 문제를 방지할 수 있을 것으로 예상된다.

또한 스케줄링 구현 로직은 최대한 멱등하게 설계/구현하는 것이 운영 및 유지보수 관점에서 효율적일 것이다.

# Reference
- [https://blog.voidmainvoid.net/262](https://blog.voidmainvoid.net/262)
- [https://tillog.netlify.app/posts/kafka-message-issue](https://tillog.netlify.app/posts/kafka-message-issue)
- [https://docs.spring.io/spring-kafka/reference/kafka/receiving-messages/listener-annotation.html](https://docs.spring.io/spring-kafka/reference/kafka/receiving-messages/listener-annotation.html)


