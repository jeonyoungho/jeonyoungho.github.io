---
title: "[MSA] Service Mesh"
date: 2024-12-29 +0800
categories: [MSA]
tags: [msa, servicemesh]
toc: true
comments: true
---

기존 모놀리틱 구조에서 MSA로 전환하면서 독립성 및 확장성과 같은 많은 이점을 얻은만큼, 아래와 같은 단점도 존재하였다.

- 서비스가 분산되어 있어, 서비스간 통신에 복잡성 증가
- 장애 추적 및 모니터링 어려움
- Service Discovery: 호출되는 서비스의 논리적, 물리적 위치가 변경되었을때 호출하는 서비스가 찾기 어려움
- Circuit Breaking: 호출되는 서비스에 문제가 있을때 다른 서비스로 전파되지 않도록 하기 위한 추가적인 노력 필요

이러한 단점들을 극복하기 위해선 각 마이크로서비스들은 서비스간 내부 통신을 통제하는 추가적인 로직이 필요하게 된다. 이러한 작업들을 각 서비스에 중복으로 넣지 않고 인프라단에서 해당 작업을 할 수 있게 하는것이 Service Mesh 이다.

# Service Mesh란?

![image](https://github.com/user-attachments/assets/379b37dc-c69c-48a5-9db8-dcddad034ba8)
_출처: https://www.redhat.com/cms/managed-files/service-mesh-1680.png_

**Service Mesh란 위에서 설명한것처럼 마이크로서비스간 모든 통신을 처리하는 인프라 계층이다.** 애플리케이션 계층이 아닌 인프라 계층에 특정 모듈을 추가하여 애플리케이션에 대한 다양한 추가 기능을 제공하는 형태이다.(라우팅, 보안, 안정성 등등)

# Service Mesh 의 세 가지 유형

## 1) Paas (Platform as a service)의 일부로 서비스 코드에 포함되는 유형
- MS Azure Service fabric 등이 해당하며, 서비스 메쉬를 구현하는데 특화된 코드가 필요하다.

## 2) 라이브러리로 구현되어 API 호출을 통해 Service mesh 에 결합되는 유형
- Spring Cloud, Netflix OSS(Ribbon/Hystrix/Eureka/Archaius) 등이 해당 유형에 해당하며, 프레임워크 라이브러리를 사용하는 형태이다. 이중 Netflix 의 Prana는 sidecar 형태로 동작한다.

## 3) Sidecar Proxy 를 이용하여 Service mesh를 마이크로서비스에 주입하는 유형
Istio/Envoy, Consul, Linkerd 등이 이 유형에 해당되며, sidecar proxy 형태로 동작된다. 따라서 서비스메시와 무관하게 코드를 작성할 수 있다.

일반적인 MSA 환경에서 아래 이미지와 같이 서비스간 직접 호출할수도 있을텐데

![image](https://github.com/user-attachments/assets/7d33bb67-7877-440a-bea1-a0253c97cac5)
_출처: https://kkang-joo.tistory.com/77_

서비스메시를 적용하면 각 서비스마다 Proxy 가 사이드카패턴으로 존재하여 서비스 내부로 들어오거나 나가는 모든 트래픽에 대한 통제가 가능해진다.

![image](https://github.com/user-attachments/assets/8f1df6e4-5fb5-4802-862c-f5dac93ff5e7)
_출처: https://kkang-joo.tistory.com/77_

다만, MSA 환경에서는 수 없이 많은 서비스들이 존재하는데, 각각의 서비스들의 호출에 대한 proxy 설정을 개별적으로 해주기는 어렵다.

이러한 문제를 해결하기 위해서, 각 proxy에 대한 설정 정보를 중앙 집중화된 컨트롤러가 통제하는 구조를 취할 수 있다.

이를 Control Plane이라 부른다.

![image](https://github.com/user-attachments/assets/dd7c623e-2759-4625-895e-75c0279840da)
_출처: https://kkang-joo.tistory.com/77_

### 사이드카 패턴

![image](https://github.com/user-attachments/assets/15f6ac5f-0564-4053-9244-e9124d24825c)
_출처: https://wonit.tistory.com/490#google_vignette_

애플리케이션 컨테이너와 독립적으로 동작하는 별도 컨테이너를 붙이는 패턴이다. 오토바이에 연결된 사이드와 유사하기 때문에 사이드카 패턴이라고 불린다. 

애플리케이션 컨테이너와 독립적으로 동작하기에 사이드카 장애 시 애플리케이션이 영향을 받지 않고, 사이드카 적용/변경/제거 등의 경우에 애플리케이션을 수정할 필요가 없다.

# Service Mesh 의 장단점

## 장점
- 비즈니스와 내부 네트워크 컴포넌트의 분리를 통해 애플리케이션 순수
로직에 집중 가능
- 마이크로서비스 내부 통신에 대한 제어 및 복잡성 이슈 해결
- 애플리케이션 외부에 구현하며 재사용 가능
- 분산된 기능의 관리를 중앙 집중화함으로써 기능 공통화를 통한 개발 효율성 확보 가능

## 단점
- 사이드카 패턴을 위한 추가 리소스 필요 (인스턴스 수 증가, 최수 2배수)
- 서비스간 통신에 네트워크 레이어 추가되어야하므로 이로 인한 지연시간이
발생합니다. (caller service -> proxy -> callee service)
- 프록시에 대한 디버깅 및 관리 이슈 증가
- 신규 기능 및 기술에 대한 러닝 커브

# 구현 기술
현재 많은 Service Mesh를 지원하는 기술들이 존재한다.

이는 [CNCF](https://landscape.cncf.io/) 에 CNCF Cloud Native Interactive Landscape 에서 더 자세히 확인할 수 있다.

- Istio
- Netflix Eureka
- Netflix Ribbon
- Hydra

# Istio
Istio는 서비스 메시(Service mesh)를 구현할 수 있는 대표적인 오픈소스 솔루션이다.
오늘날 가장 많이 사용되어 성숙하며 안정적인 서비스 메시로 인정받고 있다.

Istio를 사용하면 서비스 코드 변경 없이 로드밸런싱, 서비스 간 인증, 모니터링 등을 적용하여 마이크로 서비스를 쉽게 관리할 수 있다.

![image](https://github.com/user-attachments/assets/43fab326-2cd3-4e63-9c12-7483f1eaf513)
_출처: https://istio.io/latest/about/service-mesh_

배포된 모든 애플리케이션과 함께 프록시 "사이드카"를 추가함으로써 애플리케이션 검색, 트래픽 관리, 놀라운 관찰 가능성 및 강력한 보안 기능을 네트워크에 프로그래밍할 수 있다.

## Istio 주요 기능
- 인증: TLS 암호화, 강력한 ID 기반 인증 및 권한 부여를 통한 서비스간 통신보안 강화
- 로드밸런싱: HTTP, gRPC, WebSocket 및 TCP 트래픽에 대한 자동 부하 분산
- 제어: 액세스 제어, 풍부한 라우팅 규칙, 재시도, 장애 조치 및 장애 주입을 통해 트래픽 동작을 세밀하게 제어
- 모니터링: 클러스터 수신 및 송신을 포함하여 클러스터 내의 모든 트래픽에 대한 자동
메트릭, 로그 및 추적

## Istio 와 쿠버네티스
원래 `Istio`는 `쿠버네티스` 환경을 고려하여 설계된 오픈 소스여서 보통 쿠버네티스 환경에서 많이 사용된다. 하지만 최근엔 쿠버네티스 환경외에도 활용할수 있는 기능들이 확장되었다고 한다.

특히, `AWS ECS` 와 같은 컨테이너 관리 서비스에서도 `Istio`를 효과적으로 사용할 수 있도록 하는 다양한 방법이 제공된다고 한다.([참고](https://aws.amazon.com/ko/blogs/containers/transforming-istio-into-an-enterprise-ready-service-mesh-for-amazon-ecs/))

하지만 AWS 에는 `App Mesh` 라는 서비스매시를 구현할수있는 서비스를 제공한다. `AWS App Mesh`는 **AWS가 제공하는 네이티브 서비스 메시로, ECS 및 Fargate에 최적화**되어 있어서 보통 `AWS App Mesh` 를 사용한다. 운영 비용 측면에서는 `AWS App Mesh`가 더 간편하지만, 기능성과 확장성 면에서는 Istio가 우위를 점한다고 한다.

# Service Mesh 와 API Gateway
API Gateway와 Service Mesh와 하는 일은 동일하다.(Service Discovery, 라우팅, 트래픽 관리 등) 하지만 몇가지 차이점이 존재한다.

## 적용 위치
API Gateway는 Client-to-Server(외부 노출 o), Service Mesh는 Server-to-Server (외부 노출 x)
  
## 아키텍쳐 형태
API Gateway가 중앙집중형 아키텍쳐여서 SPOF(Single Point of Failure)을 생성한다면, Service Mesh는 분산형 아키텍쳐를 취하기 때문에 SPOF를 생성하진 않는다.

## 패턴
API Gateway는 일반적으로 Gateway proxy pattern을 사용, Service Mesh는 일반적으로 Sidecar proxy pattern을 사용

## API 게이트웨이와 서비스 메시의 통합
API 게이트웨이를 통해 클라이언트 요청을 수신하고, 서비스 메시를 통해 서비스 간의 트래픽을 제어할 수 있습니다. 이를 통해 전체 시스템의 안정성과 성능을 향상시킬 수 있다.

왜냐하면 API 게이트웨이와 서비스 메시의 통합은 각각의 장점을 결합하여 더 강력한 아키텍처를 제공하기 때문이다.

통합 구현 시에는 각 도구의 역할과 한계를 명확히 정의해야한다. 이를 통해 중복 작업을 줄이고, 효율성을 극대화할 수 있다.

또한, 통합된 모니터링과 로깅 시스템을 구축하여 시스템 상태를 실시간으로 파악할 수 있다.

### Reference
- [https://kkang-joo.tistory.com/77](https://kkang-joo.tistory.com/77)
- [https://medium.com/twolinecode/30-서비스-메시-service-mesh-란-3d769f54cd9a](https://medium.com/twolinecode/30-%EC%84%9C%EB%B9%84%EC%8A%A4-%EB%A9%94%EC%8B%9C-service-mesh-%EB%9E%80-3d769f54cd9a)
- [https://velog.io/@tedigom/MSA-제대로-이해하기-4Service-Mesh-f8k317qn1b](https://velog.io/@tedigom/MSA-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-4Service-Mesh-f8k317qn1b)
- [https://wonit.tistory.com/490#google_vignette](https://wonit.tistory.com/490#google_vignette)
- [https://yummy0102.tistory.com/372](https://yummy0102.tistory.com/372)
- [https://seing.tistory.com/193](https://seing.tistory.com/193)
- [https://jongroinf.com/news_Cloud_Docker/32367](https://jongroinf.com/news_Cloud_Docker/32367)
- [https://f-lab.kr/insight/api-gateway-service-mesh-20241218](https://f-lab.kr/insight/api-gateway-service-mesh-20241218)