---
title: "CircuitBreaker 개요 및 Resilence4J 를 활용한 실습"
date: 2023-01-20 +0800
categories: [Backend, MSA]
tags: [msa, microservicearchitecture, circuitbreaker, hystrix, resilience4j]
toc: true
comments: true
---

### 써킷 브레이커 패턴
써킷 브레이커는 누전 차단기라는 뜻을 가진다. 누전차단기는 <b>전기 회로에서 과부하가 걸리거나 단락으로 인한 피해를 막기 위해 자동으로 회로를 정지시키는 장치이다.</b>

MSA 로 구성된 환경에서 여러 마이크로서비스들이 존재하고 내부 internal-api 로 통신을 하게 되어 서비스가 운영된다.

예를 들어, 아래 그림과 같이 `Display` 마이크로서비스가 `Product`, `Delivery`, `Order` 마이크로서비스와 내부적인 통신을 가진다고 들어보자.

![image](https://user-images.githubusercontent.com/44339530/213624607-704035b9-aa0a-4842-a13a-d775be9cada2.png)

만약, `Product` 서비스에 장애가 났다고 가정해보자.

![image](https://user-images.githubusercontent.com/44339530/213624806-21f1e57d-dc28-4f90-97b4-1ae2adbb433b.png)

그럴 경우 이를 호출하는데 `Display` 마이크로서비스에도 장애가 이어져 계속 발생하게 될 것이다.

그래서 이러한 서비스들 간의 장애 전파를 방지하기 위해 적용하는 패턴이 써킷 브레이커 패턴이다.

추가적인 설명을 해보자면 외부 API 통신의 장애 전파를 막기 위해 특정 시간 동안 특정 비율의 외부 api 통신에 예외가 발생하거나 timeout이 발생하게 되면 이를 통계내어 이를 바탕으로 외부와의 통신을 차단하는 역할하게 된다.

### 써킷 브레이커의 내부 상태

[여기](https://velog.io/@hgs-study/CircuitBreaker) 포스팅에서 너무 잘 설명이 되어 있어서 그대로 인용하였다.

1) 외부 API 통신 시도

2) 외부 통신이 몇 번 실패하여 설정된 써킷 오픈 임계치를 넘을 경우, 써킷을 Open

- 위에서 언급된 외부 통신 실패는 timeout 또는 외부 api 측 예외 발생 케이스 두 가지 모두 포함될 수 있음

3) Open과 동시에 외부 서버에 요청을 날리지 않고, Fail Fast로 빠른응답 리턴

4) 써킷브레이커가 오픈하면 일정 시간 후에 반오픈(Half-Open) 상태

5) 반오픈 상태에서 다시 외부 서비스를 호출해서 장애를 확인하면 Open, 정상 응답이면 닫힘

- 위에서 "장애 확인"이라고 표현한 것은, 총(n)번 통신 중 실패율(n%)를 지정할 수 있다.
  - ex) 10번 중 50% => 10번 중 6번이 에러 발생하면 서킷브레이커 Open

![image](https://user-images.githubusercontent.com/44339530/213625568-38d38451-9d31-41fe-b9a9-64055a2a70a7.png)

### CircuitBreaker 구현체

CircuitBreaker 를 제공하는 라이브러리 중에 `Netflix Hystrix`와 `Resilience4j` 두 가지가 존재한다.

`Hystrix`는 Java 6을 기반으로 만들어졌지만 `Resilience4j`는 Java 8 기반이며 `Hystrix` 와는 다르게 다른 라이브러의 의존성이 없어서 가볍다.

Netflix Hystrix는 공식적으로 2018년 앞으로 개발을 중단하고 유지보수 상태라는 글이 명시되어 있어 `Resilience4j` 를 많이 사용하는 추세다.

> **Note**: `Resilience4j` 는 5개의 모듈(`CircuitBreaker`, `Bulkhead`, `RateLimiter`, `Retry`, `TimeLimiter`)로 구성되어 있어서 학습해보면 좋다.

간략하게 설명하면 아래와 같다.

- `CircuitBreaker`: 장애 전파를 방지하기 위해 사용
- `Bulkhead`: 동시 실행(Concurrent execution) 수를 제한하는데 사용
- `RateLimiter`: 일정 시간동안 요청 수를 제한하는데 사용
- `Retry`: 요청이 실패했을 경우 재시도 정책에 관련한 조건을 관리하기 위해 사용
- `TimeLimiter`: 원격 서버를 호출하는데 걸리는 시간을 제한

### Feign과 Resilience4j를 활용한 써킷브레이커 적용 예시

예시 프로젝트는 [깃허브 레포지토리](https://github.com/jeonyoungho/spring-cloud-resilience4j)를 참고하면 좋다.


아래는 주요 클래스이다.

```java
// Resilience4J 설정 클래스

@Configuration
public class Resilience4JConfig {

	@Bean
	public Customizer<Resilience4JCircuitBreakerFactory> globalCustomConfig() {
		CircuitBreakerConfig circuitBreakerConfig =
				CircuitBreakerConfig.custom()
									.ringBufferSizeInClosedState(5) // 닫힌 상태에서의 호출 수로, 써킷을 열어야 할지 결정할 때 사용
									.ringBufferSizeInHalfOpenState(1) // 열림 상태를 유지하는 시간(단위: 초), 해당 시간이후 반열림 상태로 변경
									.failureRateThreshold(50) // 실패한 호출에 대한 임계값(백분율), 이 값을 초과하면 써킷 오픈
									.waitDurationInOpenState(Duration.ofMillis(10000)) // 써킷 오픈 지속 시간
									.slidingWindowType(CircuitBreakerConfig.SlidingWindowType.COUNT_BASED) // 통계 집계 방식, 마지막 N번의 호출 결과를 집계, COUNT_BASED / TIME_BASED
									.build();

		TimeLimiterConfig timeLimiterConfig = TimeLimiterConfig.custom()
															   .timeoutDuration(Duration.ofSeconds(3)) // 타임 아웃 기간
															   .build();

		return factory -> factory.configureDefault(id -> new Resilience4JConfigBuilder(id)
				.timeLimiterConfig(timeLimiterConfig)
				.circuitBreakerConfig(circuitBreakerConfig)
				.build()
		);
	}
}
```

```java
// 서비스
@Slf4j
@Service
@RequiredArgsConstructor
public class ProductDisplayService {

    private final ProductRemoteFeignService productRemoteFeignService;

    public String displayProducts() {
        Optional<String> products = productRemoteFeignService.products();
        if (!products.isPresent()) {
            log.error("product api result is empty!!");
            throw new RuntimeException("products is empty!");
        }

        return products.get();
    }
}
```

```java
// product 외부 통신용 feign 정의 및 fallback factory 설정
@FeignClient(
        name = "product",
        url = "localhost:8066",
        fallbackFactory = ProductRemoteFallbackFactory.class)
public interface ProductRemoteFeignService {

    @GetMapping("/external-api/products")
    Optional<String> products();
}


@Slf4j
@Component
public class ProductRemoteFallbackFactory implements FallbackFactory<ProductRemoteFeignService> {
    @Override
    public ProductRemoteFeignService create(Throwable cause) {
        return new ProductRemoteFeignService() {

            @Override
            public Optional<String> products() {
                log.error("[ProductRemoteFallbackFactory] products API call error. " +
                        "cause = {}", cause);
                return Optional.empty();
            }
        };
    }
}
```

```java
// product 마이크로서비스 외부 통신용 api

@RestController
@RequiredArgsConstructor
@RequestMapping(path = "/external-api")
public class ExternalProductController {

    @GetMapping("/products")
    public String products() {
        throw new RuntimeException("runtime exception!!!"); // 강제 exception 발생
//        return "products exist!";
    }
}
```

위 `Resilience4J` 설정에 따라 다음과 아래와 결과가 발생한다..

<b>1) display 서비스 -> product 서비스 5번(ringBufferSizeInClosedState)까지 호출 및 exception 발생</b>

- 6번째부터 product 서비스로의 호출 차단(fallback 메서드 적용)
- slidingWindowType(통계 집계 방식)을 COUNT_BASED로 지정, 마지막 N번의 호출 결과를 집계

<b>2) DurationInOpenState 값(써킷 오픈 지속 시간) 으로 지정된 10초가 지나면 써킷 반오픈 상태로 변경</b>

- 반열림 상태에서 RingBufferSizeInHalfOpenState(반열림 상태에서의 호출 수)로 지정된 1만큼 product 서비스로 재호출
  - 만약, 성공하면 CLOSE 상태로 변경 및 실패시 다시 OPEN 상태로 변경
   
#### 출처
- [https://github.com/jeonyoungho/MSA-basics-using-Spring-cloud/blob/main/4%EA%B0%95-Circuit%20Breaker-Hystrix.md](https://github.com/jeonyoungho/MSA-basics-using-Spring-cloud/blob/main/4%EA%B0%95-Circuit%20Breaker-Hystrix.md)
- [https://velog.io/@hgs-study/CircuitBreaker](https://velog.io/@hgs-study/CircuitBreaker)
- [https://sabarada.tistory.com/118](https://sabarada.tistory.com/118)
- [https://brunch.co.kr/@springboot/262](https://brunch.co.kr/@springboot/262)

- [https://jydlove.tistory.com/71](https://jydlove.tistory.com/71)
- [https://bottom-to-top.tistory.com/57](https://bottom-to-top.tistory.com/57)
- [https://cheese10yun.github.io/resilience4j-basic/](https://cheese10yun.github.io/resilience4j-basic/)