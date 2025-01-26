---
title: "Transactional Outbox Pattern"
date: 2023-03-10 +0800
categories: [MSA]
tags: [msa, eventsource, transactionaloutboxpattern]
toc: true
comments: true
---


   
#### 출처
- [https://11st-tech.github.io/2022/05/16/junior-developer-first-msa-design-and-development/#api-%EB%A9%B1%EB%93%B1%EC%84%B1%EA%B3%BC-%EB%8D%B0%EC%9D%B4%ED%84%B0%EC%9D%98-%EC%88%9C%EC%84%9C-%EB%B3%B4%EC%9E%A5](https://11st-tech.github.io/2022/05/16/junior-developer-first-msa-design-and-development/#api-%EB%A9%B1%EB%93%B1%EC%84%B1%EA%B3%BC-%EB%8D%B0%EC%9D%B4%ED%84%B0%EC%9D%98-%EC%88%9C%EC%84%9C-%EB%B3%B4%EC%9E%A5)
- [https://devocean.sk.com/blog/techBoardDetail.do?ID=164096](https://devocean.sk.com/blog/techBoardDetail.do?ID=164096)
- [https://www.popit.kr/msa%EC%97%90%EC%84%9C-%EB%A9%94%EC%8B%9C%EC%A7%95-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0/](https://www.popit.kr/msa%EC%97%90%EC%84%9C-%EB%A9%94%EC%8B%9C%EC%A7%95-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0/)

- [https://www.daddyprogrammer.org/post/14068/database-migration-by-transactional-outbox/](https://www.daddyprogrammer.org/post/14068/database-migration-by-transactional-outbox/)
- [https://www.popit.kr/msa%EC%97%90%EC%84%9C-%EB%A9%94%EC%8B%9C%EC%A7%95-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0/](https://www.popit.kr/msa%EC%97%90%EC%84%9C-%EB%A9%94%EC%8B%9C%EC%A7%95-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0/)
- [https://americanopeople.tistory.com/398](https://americanopeople.tistory.com/398)
- [https://velog.io/@giantim/7](https://velog.io/@giantim/7)
- [https://happy-coding-day.tistory.com/entry/Message-Relay-%EB%A5%BC-Polling-publiser-%EB%B0%A9%EC%8B%9D%EC%9C%BC%EB%A1%9C-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0](https://happy-coding-day.tistory.com/entry/Message-Relay-%EB%A5%BC-Polling-publiser-%EB%B0%A9%EC%8B%9D%EC%9C%BC%EB%A1%9C-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)
- [https://feco.tistory.com/128](https://feco.tistory.com/128)
- []()