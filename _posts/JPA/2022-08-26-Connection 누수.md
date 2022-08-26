---
title: "[JPA] Connection 누수" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-08-26 +0800
categories: [JPA] # categories는 최대 2개까지 가능
tags: [hikaricp, connection, leak] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

사용한 커넥션을 커넥션풀로 다시 반환하지 못하게 되는 현상을 `커넥션 누수`라 한다.
아래 출처를 통해 다양한 포스팅들을 참고하면 이를 이해하기 쉬울 것이다.
특히! Hibernate 멀티테넌시를 사용한다면 더욱 주의가 필요할 것이다 :)

#### 참고 및 출처
- [https://velog.io/@rnjsrntkd95/Hikari-CP-%EC%BB%A4%EB%84%A5%EC%85%98-%EB%88%84%EC%88%98-with.%EB%A9%80%ED%8B%B0%ED%85%8C%EB%84%8C%EC%8B%9C](https://velog.io/@rnjsrntkd95/Hikari-CP-%EC%BB%A4%EB%84%A5%EC%85%98-%EB%88%84%EC%88%98-with.%EB%A9%80%ED%8B%B0%ED%85%8C%EB%84%8C%EC%8B%9C)
- [https://imksh.com/73#%25EC%25--%25-C%25EC%25--%25--%25EC%25--%25-C%25--%25EB%25A-%25-C%25EB%25A-%25-C%25EB%25--%25--%25EA%25B-%25A-%25--%25EC%25-E%25--%25EC%25-B%25--%25--%25EB%25B-%25--%25ED%25--%25--%25--%25ED%25--%25--%25EA%25B-%25A-%25EC%25A-%25-----](https://imksh.com/73#%25EC%25--%25-C%25EC%25--%25--%25EC%25--%25-C%25--%25EB%25A-%25-C%25EB%25A-%25-C%25EB%25--%25--%25EA%25B-%25A-%25--%25EC%25-E%25--%25EC%25-B%25--%25--%25EB%25B-%25--%25ED%25--%25--%25--%25ED%25--%25--%25EA%25B-%25A-%25EC%25A-%25-----)
- [https://techblog.woowahan.com/2664/](https://techblog.woowahan.com/2664/)
- [https://do-study.tistory.com/97](https://do-study.tistory.com/97)
- [https://velog.io/@miot2j/Spring-DB%EC%BB%A4%EB%84%A5%EC%85%98%ED%92%80%EA%B3%BC-Hikari-CP-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0](https://velog.io/@miot2j/Spring-DB%EC%BB%A4%EB%84%A5%EC%85%98%ED%92%80%EA%B3%BC-Hikari-CP-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)
- [https://okky.kr/articles/728516?note=2003413](https://okky.kr/articles/728516?note=2003413)
- [https://velog.io/@aram/Spring-Boot-HikariCP-log-%EC%B6%9C%EB%A0%A5-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0](https://velog.io/@aram/Spring-Boot-HikariCP-log-%EC%B6%9C%EB%A0%A5-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)
- [https://kwonnam.pe.kr/wiki/java/database/hikaricp](https://kwonnam.pe.kr/wiki/java/database/hikaricp)
