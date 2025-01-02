---
title: "[Database] Master&Slave Replication"
date: 2024-12-30 +0800
categories: [Database]
tags: [database, mysql, mariadb, masterslave, mha]
toc: true
comments: true
---

고가용성 시스템을 구축하려면 DB 이중화는 필수적인 요소이다.

DB 이중화 방식에는 두 가지가 존재한다.

- Clustering: 여러 개의 DB를 수평적인 구조로 구축하는 방식, 동기 방식으로 노드들 간의 데이터를 동기화
- Replication: 여러 개의 DB를 권한에 따라 수직적인 구조(Master-Slave)로 구축하는 방식, 비동기 방식으로 노드들 간의 데이터를 동기화

좀 더 자세한 내용은 [여기](https://mangkyu.tistory.com/97)에 자세히 잘 정리되어 있으니 참고하면 좋다.

# Master & Slave Replication
DB 이중화 방식중 가장 흔히 사용되는 방식은 Master-Slave Replication 방식이다.

복제 기반 이중화의 대표적인 방법으로 Master 서버에서 변경된 데이터를 Slave 서버로 **비동기적으로 복제**하여 이중화 구성을 구현하는 방법이다.

# 복제 매커니즘
실제 이중화된 DB 의 형상을 복잡하는 매커니즘은 아래와 같다.

- Master DB에서 DATA변경이 일어나면(1), 이를 Master DB에 반영합니다(2).
- 변경이력을 Binary Log로 저장합니다(3).
- 관련 이벤트를 Slave들에게 넘깁니다(4).
- Slave IO Thread에서 이벤트를 캐치하면(5) Binary Log를 Slave 각각의 Relay Log에 저장합니다(6).
- Slave SQL Thread에서 Relay Log를 읽어(7) Slave를 업데이트합니다(8).
- 읽기 처리를 할 때는 Slave를 사용합니다.

![image](https://github.com/user-attachments/assets/63a94b47-c197-425f-be17-977980ec3a29)
_출처: https://jung-story.tistory.com/118_

**일반적으로 DB 트래픽 분산을 위해서 Mysql Replication을 통해서 트래픽 집중 문제를 해결할 수 있습니다.**

![image](https://github.com/user-attachments/assets/e62c1e7a-59aa-4259-aa86-7592fecc9cdf)
_출처: https://jung-story.tistory.com/118_

Master에게는 데이터 동시성이 아주 높게 요구되는 트랜잭션을 담당하고, Slave에게는 읽기만 데이터 동시성이 꼭 보장될 필요는 없는 경우에 읽기 전용으로 데이터를 가져오게 됩니다.

일반적으로 Front에서 데이터를 읽어들일 때, 꼭 데이터 일관성이 필요한 경우와 아닌 경우에 대한 API가 나누어지게 됩니다.

그런 경우에 읽기전용으로 트랜잭션을 사용하여 디비에 대한 트래픽을 분산하여 가용성 높은 시스템을 구축할 수 있다.

# 장애 발생 시나리오

## 1. Slave 일부가 죽었을 경우
- Replication Driver에서 자동으로 감지하고 살아있는 다른 Slave로 데이터 요청쿼리를 보낸다.
  - Replication Driver는 DB 연결을 관리하는 특별한 드라이버이다.
  - 주기적으로 각 Slave의 상태를 체크하는 HealthCheck 기능이 있다.
  - Slave가 응답하지 않으면 해당 Slave를 연결 목록에서 제외한다.
  - 읽기 요청이 오면 자동으로 살아있는 다른 Slave로 쿼리를 라우팅한다.
  - 이 과정이 자동으로 이루어지므로 애플리케이션 코드를 수정할 필요가 없게된다.
- DB 접속을 공인 아이피로 통신할 지라도 접속 지연 현상은 발생하지 않는다.
  - Replication Driver는 연결 풀(Connection Pool)을 관리한다.
  - 각 Slave에 대한 연결을 미리 생성하고 유지한다.
  - 새로운 Slave로 전환할 때도 이미 생성된 연결 풀을 사용한다.
  - 따라서 새로운 연결을 설정하는 오버헤드가 발생하지 않는다.
  - DNS 조회나 TCP 연결 설정 같은 지연 요소를 미리 처리했기 때문에 빠른 전환이 가능하다.

## 2. Slave가 모두 죽었을 경우
- Master와만 통신이 가능할 뿐 Slave와는 통신이 불가하다.
- Slave가 정상적으로 돌아오게 되면 자동으로 상태가 복구된다.

## 3. Master 죽었을 경우
- Slave가 멀쩡하게 살아있는데도 데이터를 읽을 수가 없음 → 전체 서비스 마비
  - **기본적인 설정에서는 Master가 다운되면 전체 복제 시스템이 정지된다.**
  - Slave들은 Master로부터 변경사항을 지속적으로 동기화해야 하는데, Master 연결이 끊기면 더 이상 동기화가 불가능해진다.
  - Slave들이 최신 데이터를 가지고 있는지 확신할 수 없게 된다.
  - 따라서 데이터 일관성을 보장하기 위해 읽기 작업도 중단된다.
- Master를 복구하였다 할 지라도, 서비스가 정상적으로 돌아오지 않음 → 데이터 유실
  - Master가 다운된 동안 처리되지 못한 트랜잭션들이 있을 수 있다.
  - Master 복구 후 Slave와 동기화 과정에서 불일치가 발생할 수 있다.
  - 특히 Master가 비정상 종료된 경우, 마지막 체크포인트 이후의 데이터가 유실될 수 있다.

# 장애 복구 솔루션

DB 이중화시의 장애 복구 솔루션으로는 MMM(Multi-Master Replication Manager)과 MHA(Master High Availability)가 있다.

보다 더 자세한 설명은 [여기](https://jwdeveloper.tistory.com/215)에 자세히 설명되어 있다.

## MMM(Multi-Master Replication Manager)
- Active-Active 혹은 Active-Stanby 형태의 두 대 이상의 Master DB 로 구성된다.
- Active-Active 형태는 Dual Active Master 라고도 불린다. 두 개의 Master DB 모두 쓰기 작업이 가능해지다보니 발생하는 데이터 충돌 문제, 설정 및 운영의 복잡성 문제로 잘 사용되지 않는것 같다.

![image](https://github.com/user-attachments/assets/2e5a8531-fd56-46c8-a89a-07b5cb6fec76)
_출처: https://jwdeveloper.tistory.com/215_

- Active-Stanby 형태는 위 이미지와 같이 두 대의 Master 를 두어 양방향 복제를 시키는데 형태인데 보통 Master(Standby)는 데이터가 변경되지 않도록 MMM 모니터로부터 읽기모드로 제어시킨다.

![image](https://github.com/user-attachments/assets/b74ae0dd-9951-4476-a7dd-196411abd151)
_출처: https://jwdeveloper.tistory.com/215_

- Slave가 추가된다면 단방향 복제의 Slave가 하나씩 추가되는 구조이며, 이역시 Master(Active) 를 제외하고는 모두 읽기모드로 제어된다.
- Active 상태의 Master 서버에 장애가 발생했을 때 Master(Standby)가 Master(Active)로 승격되어 Failover 되는 방식으로 동작한다. 세부적인 프로세스에 대해선 [여기](https://jwdeveloper.tistory.com/215)에 자세히 설명되어 있다.

## MHA(Master High Availability)
https://rachel0115.tistory.com/entry/MariaDB-Replication-%EC%A0%81%EC%9A%A9%EA%B8%B0-4-MHA
https://onestone-note.tistory.com/34

![image](https://github.com/user-attachments/assets/c7a1768d-97fa-40b2-aaf1-9ff3d0691215)
_출처: https://jwdeveloper.tistory.com/215_

- 하나의 Master 와 N개의 Slave로 구성되어 있고, 모두 단방향 복제로 이루어져 있다.
- MHA Manager, Master, Slave 서버 총 3개가 기본 구성이며 상황에 따라서는 1개의 Master와 N개의 Salve 최소 2대 까지 사용할 수 있다.

![image](https://github.com/user-attachments/assets/711b20e2-8726-4ae9-85cf-afad6c78a5e6)
_출처: https://jwdeveloper.tistory.com/215_

- Master 장애로 서비스가 불가능한 상태가 되면,  자동으로 failover를 수행하여 (가장 최신 데이터가 동기화된) Slave를 Master로 승격시켜 서비스 다운타임을 최소화하여 failover 한다.
- 서비스가 동작안하는 다운 타임을 최소화하는게 핵심이다.
- Master 장애로 각 노드(Master 및 Slave)의 데이터 불일치가 발생하지 않는다. MHA Manager(Monitor)가 Master의 Binary Log와 Slave들의 Relay Log를 확인하여 DB 간의 차이나는 쿼리를 DB에 반영시키기 때문이다. (Master의 변경 기록 확인)

# MariaDB 기반 이중화 구성
- [https://velog.io/@oksk4753/Docker를-이용한-Mysql-MariaDB-단방향-이중화-구성](https://velog.io/@oksk4753/Docker%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-Mysql-MariaDB-%EB%8B%A8%EB%B0%A9%ED%96%A5-%EC%9D%B4%EC%A4%91%ED%99%94-%EA%B5%AC%EC%84%B1)
- [https://onestone-note.tistory.com/34](https://onestone-note.tistory.com/34)

#### Reference
- [https://mangkyu.tistory.com/97](https://mangkyu.tistory.com/97)
- [https://jung-story.tistory.com/118](https://jung-story.tistory.com/118)
- [https://velog.io/@sweet_sumin/DB-Master-Slave-개념](https://velog.io/@sweet_sumin/DB-Master-Slave-%EA%B0%9C%EB%85%90)
- [https://gorilla-ohgiraffers.tistory.com/8](https://gorilla-ohgiraffers.tistory.com/8)
- [https://cl8d.tistory.com/130](https://cl8d.tistory.com/130)
- [https://cornswrold.tistory.com/561#google_vignette](https://cornswrold.tistory.com/561#google_vignette)
- [https://code-killer.tistory.com/200](https://code-killer.tistory.com/200)
- [https://jwdeveloper.tistory.com/215](https://jwdeveloper.tistory.com/215)