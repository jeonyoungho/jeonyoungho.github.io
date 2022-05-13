---
title: "[HTML5] 시맨틱 요소와 검색 엔진" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-08-26 +0800
categories: [PoiemaWeb, HTML] # categories는 최대 2개까지 가능
tags: [poiemaweb, html5] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# [시멘틱 웹(Semantic Web)](https://poiemaweb.com/html5-semantic-web)

~~~
<font size="6"><b>Hello</b></font>
<h1>Hello</h1>
~~~

<font size="6"><b>Hello</b></font>
<h1>Hello</h1>

위의 코드를 보면 1행과 2행은 브라우저에서 동일한 외형을 갖는다. 이는 h1태그의 디폴트 스타일이 1행과 같기 때문이다.

그러나 1행의 요소는 의미론적으론 어떤 의미도 가지고 있지 않다. 그러나 2행의 요소는 header(제목) 중 가장 상위 레벨이라는 의미를 내포하고 있어서 개발자가 의도한 요소의 의미가 명확히 드러나고 있다.이것은 코드의 가독성을 높이고 유지보수를 쉽게한다.

<b>즉, 시멘틱 태그란 브라우저, 검색엔진, 개발자 모두에게 콘텐츠의 의미를 명확히 설명하는 역할을 한다.</b>

<b>또한, 시맨틱 웹이란 웹에 존재하는 수많은 웹페이지들에 메타데이터(Metadata)를 부여하여, 기존의 잡다한 데이터 집합이었던 웹페이지를 ‘의미’와 ‘관련성’을 가지는 거대한 데이터베이스로 구축하고자 하는 발상이다.</b>

HTML 요소는 non-semantic 요소, semantic 요소로 구분할 수 있다.

~~~
non-semantic 요소
div, span 등이 있으며 이들 태그는 content에 대하여 어떤 설명도 하지 않는다.

semantic 요소
form, table, img 등이 있으며 이들 태그는 content의 의미를 명확히 설명한다,
~~~

|tag|description|
|-|------|
|nav|네이베이션을 의미한다|
|aside|사이드에 위치하는 공간을 의미한다|
|section|본문의 여러 내용(article)을 포함하는 공간을 의미한다|
|article|본문의 주내용이 들어가는 공간을 의미한다|
|footer|푸터를 의미한다|

![image](https://user-images.githubusercontent.com/44339530/130905573-be504935-f16a-4527-92f2-63c2ee7ace2b.png)

#### 출처
- [https://poiemaweb.com/html5-semantic-web](https://poiemaweb.com/html5-semantic-web)