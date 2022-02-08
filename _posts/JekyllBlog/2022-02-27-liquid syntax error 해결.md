---
title: "[Jekyll 블로그] liquid syntax error 해결하는 법" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-06-21 00:00:00 +0800
categories: [Jekyll] # categories는 최대 2개까지 가능
tags: [blog, jekyll, github] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

<img width="479" alt="스크린샷 2022-02-07 오전 12 52 32" src="https://user-images.githubusercontent.com/44339530/152689137-4b44187c-ee5b-4078-89f9-5c12fa479cf4.png">

포스팅을 작성 후 push 를 했으나 빌드하는 과정에서 다음과 같은 `liquid syntax error` 가 발생하였다. 구글을 통해 쉽게 원인과 해결책을 찾아내어 해결할 수 있었다.

# 원인
Jekyll에서 사용되는 liquid는 {% raw %}`{{`와 `}}`{% endraw %} 를 escape 문자로 사용하는데, md문서에 {% raw %}`{{`, `}}`{% endraw %} 가 있는 경우 에러 메시지를 출력하기 때문이다.

# 해결 방법

<img width="246" alt="스크린샷 2022-02-07 오전 12 55 09" src="https://user-images.githubusercontent.com/44339530/152689300-a2136fc9-26eb-499d-89b6-343a26e6c928.png">

위 이미지 같이 여는 중괄호가 시작하기 전에 raw를, 뒤에는 endraw를 추가하면 된다.

#### 출처
- [https://iamheesoo.github.io/blog/gitblog-sol-jekyll02](https://iamheesoo.github.io/blog/gitblog-sol-jekyll02)
- [https://dongsik93.github.io/til/2019/12/03/til-jekyll-liquid-warning/](https://dongsik93.github.io/til/2019/12/03/til-jekyll-liquid-warning/)
- [https://jmjeong.com/escape-in-liquid-syntax/](https://jmjeong.com/escape-in-liquid-syntax/)