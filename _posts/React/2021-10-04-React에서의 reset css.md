---
title: "[React] reset css" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-10-04 +0800
categories: [React] # categories는 최대 2개까지 가능
tags: [react] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

html로 코드를 작성하고 브라우저로 실행해보면 적용하지 않았는데도 불구하고, 자동으로 style(margin, padding 등)이 들어가게 될 것이다.

<b>웹 브라우저마다 default 값으로 스타일이 적용되어 있기 때문이다.</b>

브라우저마다의 기본 디폴트 스타일 값이 아니라 동일한 CSS 스타일을 보여주기 위해 이런 default 디폴트 값을 초기화 해주어야 한다.

실제 적용 방법은 아래 출처 또는 [여기](https://cssdeck.com/blog/scripts/eric-meyer-reset-css/)를 참고하자.

#### 출처
- [HwanE Develop Blog](https://parkjh7764.tistory.com/80)
- [https://velog.io/@eunjeong/React-Style-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0](https://velog.io/@eunjeong/React-Style-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)
- [https://bigstar-vlog.tistory.com/48](https://bigstar-vlog.tistory.com/48)
- [https://catstanets.tistory.com/122](https://catstanets.tistory.com/122)
- [https://react.vlpt.us/styling/03-styled-components.html](https://react.vlpt.us/styling/03-styled-components.html)