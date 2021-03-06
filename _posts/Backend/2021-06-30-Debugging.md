---
title: "[Backend] Debugging(디버깅)" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-30 +0800
categories: [Backend] # categories는 최대 2개까지 가능
tags: [debugging] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# 디버깅이란?
- 디버깅의 뜻은 프로그램 실행 시 문제를 일으키는 오류, 즉 <b>'버그'</b>를 찾아 해결하는 과정을 의미한다.
- 이 <b>버그</b>는 크게 <b>문법적 오류</b>와 <b>논리적 오류</b>로 분류할 수 있다.
    - <b>문법적 오류<b>는 말 그대로 프로그래밍 언어의 문법에 맞지 않는 코드가 있을 경우 발생하며 대체로 IDE로 해결할 수 있기에 디버깅이 쉬운편이다.
    - <b>논리적 오류</b>는 문법적으로 문제가 없지만, 의도와는 맞지 않게 프로그램이 실행되는 오류이다. 이 부분은 IDE의 기능만으로는 해결되지 않으며, 프로그래머가 가장 골치아파하는 오류이다.

#### 디버깅 용어의 유래
- "디버깅"이라는 단어는 프로그래머, 그레이스 호퍼에 의해 만들어진 단어이다. 어느 날, 호퍼는 Mark Ⅱ 컴퓨터에서 오작동을 발견하였고, 그 원인이 나방임을 알아내 컴퓨터에서 핀셋으로 꺼냇다.(세계 최초 디버깅) '버그'란 단어는 1800년대부터 기술적인 오류를 가리키는 말로도 사용되었지만, '디버깅'이라는 단어는 이때 처음으로 사용되었다.

# 디버깅의 중요성
- <b>디버그라는 동사의 첫 번째 의미는 오류를 제거하는 것이지만 더 중요한 의미는 프로그램을 조사하며 프로그램이 실행되는 과정을 들여다 본다는 것이다.</b>
- 대규모 프로젝트의 코드들은 대부분 문서화도 완벽하게 돼 있지 않고 코드양도 굉장히 많을 것이다. 이러한 코드가 실행되는 과정에 대한 가시성을 확보할 수 없다면 사소한 문제로도 난관에 부딪히게 될 것이다. 이러한 가시성은 디버깅을 통해서만 확보할 수 있을때가 많다.
- 위와 같은 이유로 인해 프로그래밍에 있어 디버깅은 필수적인 요소이며 실제 개발할 때에도 가장 중요한 부분이다. 디버깅을 얼마나 잘하느냐에 따라 프로그래머의 실력이 갈릴 정도로 프로그래머에게 있어 가장 중요한 능력이 디버깅 능력이다.

#### 출처
- [https://bakjh6280.wordpress.com/2018/04/22/what-is-debugging/](https://bakjh6280.wordpress.com/2018/04/22/what-is-debugging/)
- [https://codingnuri.com/learn-to-debug/](https://codingnuri.com/learn-to-debug/)