---
title: "[JPA] default_batch_fetch_size"
date: 2021-11-21 +0800
categories: [Backend, JPA]
tags: [spring, jpa]
toc: true
comments: true
---

JPA를 사용할 때 N+1 문제를 해결하기 위해 default_batch_fetch_size를 설정하여 in절로 여러 개를 한 번에 가져올 수 있도록 한다.

<b>하지만 실제 default_batch_fetch_size를 100으로 설정하고 30개의 데이터를 가져오려 하니 25개를 담은 in절 쿼리 1번 + 5개를 담은 in절 쿼리 한 번이 호출되었다.</b>


# Statement와 PreparedStatement
RDB의 경우 쿼리가 호출 될 때 아래의 3가지의 과정을 거친다.

~~~
1) 쿼리 문장 분석
2) 컴파일
3) 실행
~~~

Statement를 사용할 경우 매번 쿼리가 수행할 때마다 위의 3단계를 거치게 되고, PreparedStatement는 처음 한 번만 세 단 계를 거친 후 캐시에 담아 이후부턴 재사용을 한다. 그러므로 만약 동일한 쿼리를 반복적으로 수행한다면 PreparedStatment가 DB에 훨씬 적은 부하를 주며, 성능도 좋을 것이다.

# 그럼 처음으로 돌아가서 왜 이런 현상이 나타날까?
만약 in절로 가져올 데이터가 1개, 2개, 3개, 100개가 있으면 모두 각각 다음 처럼 최대 100개의 preparedstatement 쿼리를 만들어야 한다.

~~~
select * from x where in (?)

select * from x where in (?, ?)

select * from x where in (?, ?, ?)

select * from x where in (?, ?, ? ...)
~~~

이렇게 되면 DB 입장에서 너무 많은 preparedstatement 쿼리를 캐싱해야 하고, 성능도 떨어지게 된다. 그래서 하이버네이트는 이 문제를 해결하기 위해 내부에서 나름 최적화를 합니다.

~~~
100 = 설정값

50 = 100/2

25 = 50/2

12 = 25/2

1~10 = 자주 사용하니 모두 저장
~~~

이런식으로 잡아두게 되어 30개를 가져온다 했을 때 25개를 한 번에 가져오고 나머지 5개를 다음에 가져오게 되는 것이다.

# batch_fetch_style
`batch_fetch_style` 프로퍼티를 지정하여 위와 같은 최적화 전략을 컨트롤할 수 있다.

- batch_fetch_style: legacy // 기본 (위와 같은 방식으로 최적화)
- batch_fetch_style: padded // 패딩값을 넣어 파라미터 개수보다 한단계 큰 배치크기를 사용
- batch_fetch_style: dynamic // 최적화x (배치크기 무시하고 실제 파라미터 개수대로 작동)

#### 출처
- [https://www.inflearn.com/questions/34469](https://www.inflearn.com/questions/34469)
- [https://velog.io/@jadenkim5179/Spring-defaultbatchfetchsize%EC%9D%98-%EC%9E%91%EB%8F%99%EC%9B%90%EB%A6%AC](https://velog.io/@jadenkim5179/Spring-defaultbatchfetchsize%EC%9D%98-%EC%9E%91%EB%8F%99%EC%9B%90%EB%A6%AC)