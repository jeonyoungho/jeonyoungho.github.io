---
title: "[Javascript] innerText와 innerHTML 차이점" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-20 +0800
categories: [Frontend] # categories는 최대 2개까지 가능
tags: [jekyll, codepen, devlog] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

innerText와 innerHTML은 단순한 텍스트만 다룰 경우에는 차이가 없어 보인다.
이 두 속성은 다루는 값이 text element인지, html element인지에 따라 차이가 난다.

# 값 가져오기 (innerText vs innerHTML)

## element.innerText;
element 안의 text 값들만을 가져옵니다.

## element.innerHTML;
innerText와는 달리 innerHTML은 element 안의 HTML이나 XML을 가져옵니다.

# 값 설정하기 (innerText vs innerHTML)

## element.innerText = "<div style='color:red'>A</div>";
element.innerText에 html을 포함한 문자열을 입력하면, html코드가 문자열 그대로 element안에 포함됩니다.

## element.innerHTML = "<div style='color:red'>A</div>";
element.innerHTML 속성에 html코드를 입력하면, html element가 element안에 포함되게 됩니다.

#### 출처
- [어제 오늘 내일](https://hianna.tistory.com/480)