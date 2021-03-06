---
title: "[Backend] Semantic Versioning" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [Backend] # categories는 최대 2개까지 가능
tags: [versioncontrol, semantic-versioning] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Versioning?
- 소프트웨어의 패키지가 새롭게 업데이트 될 때, 생각보다 다양한 문제에 직면하게 된다. 기능의 사용법을 바꾸어버리거나 동작 방식의 변경 같은 변화들은 그에 의존하는 다른 소프트웨어를 의도대로 동작하지 못하게 하므로, 새로운 변화와 기존의 것을 구분할 필요가 생겼다.
- 이로 인해, 버전이라는 개념은 소프트웨어 패키지의 변화를 구분하기 위해 사용한다.

# Semantic versioning?
- 기존의 버전 명시 방법은 모든 소프트웨어마다 제 각각 미묘하게 다르다는 문제점이 있음
- 위의 문제점을 해결하기 위해 Github의 공동창업자인 Tom Preston-Werner가 제안한 버전 명시 방법이다.
- RFC2119에 의해 규칙을 표기하여 의미적 엄격함을 높이고, 패키지 개발 생명주기에 발생할 수 있는 여러 상황을 포괄적으로 담아 일관성과 유선성을 균형 있게 갖추고 있다.

## Major.Minor.Patch
- Semantic versioning의 구조
- Major: 기존 버전과 호환되지 않게 API가 바뀔때 올린다. 즉, 전체적으로 모든 것이 바꼈을 때 사용한다.
- Minor: 기존 버전과 호환되면서 새로운 기능을 추가할 때 올린다. 즉, 기능 추가랑 사소한 변경이 발생했을 때 사용한다.
- Major: 기존 버전과 호환되면서 버그를 수정한 것일때 올린다. 즉, 버그를 수정했을 때 사용한다.

## 일반적인 규칙
- 1) 버전 번호는 Major, Minor, Patch 의 형태로 배포하고, Major, Minor, Patch 는 각각 자연수이고 절대 앞에 0이 붙어서는 안된다.
- 2) 각 번호의 수는 항상 증가해야 한다.
- 3) 특정 버전으로 패키지를 배포하고 나면, 그 버전의 내용은 절대 변경하지 말아야한다. 변경분이 있다면 반드시 새로운 버전으로 배포하도록 한다.
- 4) Major 버전이 변경될 때, Minor, Patch 는 0으로 초기화 된다.
- 5) Minor 버전이 변경될 때, Patch 는 0으로 초기화 된다.

## Major 버전 증가
- 하위 버전과 호환되지 않는 변화가 생겼을 때
- 대대적인 변화가 일어났을 때
- 클라이언트가 1.0.0 버전의 API 접근 방식으로 2.0.0 버전에 접속할 수 없을때

## Minor 버전 증가
- 하위 버전과 호환되면서, 새로운 기능이 추가 될 때
- 새로운 기능이 추가된 API가 나왔지만, 기존의 공개된 API가 하위 호환되고 있을 때
- 기존의 기능이 변경되거나 사용 방법이 변경 되었을 때

## Patch 버전 증가
- 버그 수정
- 기존 클라이언트가 알아차리지 못할 정도의 작은 변화가 있을 때
- 서버 코드 내부적으로 소스가 수정되었을때

## Semantic versioning의 공식 홈페이지
- 위에서 설명하지 않은 다른 규칙들도 존재하니 Semantic Versioning의 공식 홈페이지를 통해 참고하면 된다.
- [https://semver.org/lang/ko/](https://semver.org/lang/ko/)

#### 출처
- [https://kiwinam.com/posts/33/version-naming/](https://kiwinam.com/posts/33/version-naming/)
- [https://spoqa.github.io/2012/12/18/semantic-versioning.html](https://spoqa.github.io/2012/12/18/semantic-versioning.html)