---
title: "Transactional Outbox 패턴"
date: 2025-06-03 +0800
categories: [Backend, Kafka]
tags: [kafka, domainevent]
toc: true
comments: true
published: false
---

Transactional Outbox 패턴은 이벤트 기반 아키텍처를 적용할때 로컬 트랜잭션과 이벤트(메시지) 발행 간의 원자성 보장 문제를 해결하는 데 사용된다.

