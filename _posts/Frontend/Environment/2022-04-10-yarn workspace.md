---
title: "yarn workspace" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-04-10 +0800
categories: [Frontend, Environment] # categories는 최대 2개까지 가능
tags: [yarn, monorepo] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

모노레포에 대해선 [여기]()에서 알아보았다.

# yarn workspace 사용법
모노레포를 구성하는 방법 중에 `yarn workspace` 로 구성하는 방법을 알아보자.

들어가기에 앞서 yarn 의 workspace 기능을 통해서 MonoRepo 를 구성하는 방식은 아주 간단하다.

먼저 아래 커맨들를 실행하여 npm프로젝트를 초기화해주자.

```
yarn init
```

그리고 `package.json` 에 아래와 같이 `private` 과 `workspaces Property`를 설정하면 된다.

```json
{
  ...
  "private": true,
  "workspaces": [
    "packages/*"
  ],
  ...
}
```

# Dependency 관리 방식
위 방식대로 설정을 마치고, 패키지를 구성한 뒤 의존성을 설치하게 되면, 패키지 각각의 패키지에서 요구하는 모듈의 버전을 확인하여 공통되는 부분은 root의 `node_modules` 폴더 안에 설치를 하고, `symlink`를 통해 각각의 패키지로 연결하여 사용하면 된다.

![images_sms8377_post_013fe4e6-cb7d-4411-8ad2-6442e5b0291b_image](https://user-images.githubusercontent.com/44339530/163669351-be5d4550-5bfa-4bc1-b477-ec434cb212d9.png)
_출처: https://velog.io/@sms8377/DevOps-MonoRepo%EB%9E%80_

# 패키지 관리
패키지를 추가하기 위해 그 프로젝트 디렉토리로 들어갈 필요없이 프로젝트 루트에서 관리를 할 수 있다.

```
yarn workspace [패키지 이름] add 라이브러리명 [옵션]

yarn workspace my-sub-package add typescript -D
```

만약 root package.json을 통해 관리를 하고자 하는 경우엔 아래와 같은 커맨드를 실행하며 된다. 그러면 루트 `node_modules`에서 관리되어진다.

```
yarn add 라이브러리명 -W -D

yarn add typescript -W -D
```

특정 패키지에서 외부 라이브러리 제거시엔 다음과 같이 사용하면 된다.

```
yarn workspaces [패키지 이름] remove [라이브러리명]

yarn workspaces web-project remove some-package
// web-project에서 some-package를 제거한다.
```

# 만약 A, B 패키지에서 동일한 버전의 라이브러리를 포함한다면?

<img width="997" alt="스크린샷 2022-04-16 오후 6 30 15" src="https://user-images.githubusercontent.com/44339530/163669825-e8df3ca2-701c-45d3-b5d2-709e046eccbb.png">

아래와 같이 두 개의 패키지에서 동일한 버전(^0.2.5)의 `react-lottie` 라이브러리를 내려받을 경우엔 자동적으로 root `node_modules`에 받게 된다.

## 그러면 A, B 패키지에서 다른 버전의 동일한 라이브러리를 포함한다면?

- `construction-map-client-web` 패키지: `react-lottie: 0.2.5`
- `widget`: `react-lottie: 1.2.3`

<img width="929" alt="스크린샷 2022-04-16 오후 6 46 34" src="https://user-images.githubusercontent.com/44339530/163670349-344726e0-1957-410e-a264-6fe46176364f.png">

위처럼 패키지를 설치하면 root `node_modules`에는 더 오래된 버전의 라이브러리가 설치되고, 더 최신 버전이 명시된 widget에는 직접 1.2.3 버전이 설치된 것을 확인할 수 있다.

## 패키지 참조는 어떻게 이뤄지는가?
예를 들어, `worksapce-b`에서 `workspace-a`를 참조한다고 가정해보자. 그러면 `package.json`은 아래와 같을 것이다.

- worksapce-a/package.json

```json
{
  "name": "workspace-a",
  "version": "1.0.0",

  "dependencies": {
    "cross-env": "5.0.5"
  }
}
```

- worksapce-b/package.json

```json
{
  "name": "workspace-b",
  "version": "1.0.0",

  "dependencies": {
    "cross-env": "5.0.5",
    "workspace-a": "1.0.0"
  }
}
```

결과는 다음과 같을 것이다.

root `node_modules`
- cross-env
- workspace-a -> packages/worksapce-a

`yarn workspace`가 패키지 관리 방식을 정리해보면 다음과 같다.

프로젝트 루트에서 yarn install을 실행하면 루트의 package.json안에 명시되어 있는 dependency, 그리고 각 패키지에 명시되어있는 dependency가 중복을 최대한 줄인 채 루트의 node_modules 안에 `호이스팅` 되어서 설치되고 dependency로 명시 되어있는 모듈은 `심링크`가 걸려 npm에 배포되어있는 버전이 아니라 로컬에 있는 코드를 바로 볼 수 있게 해준다.

![1_uGcDMXSuT1LYa0Ex8kOuaw](https://user-images.githubusercontent.com/44339530/163670495-1f418f8c-20ea-4b57-8cae-1c7cf9ab473a.png)

_https://classic.yarnpkg.com/_

<img width="894" alt="스크린샷 2022-04-16 오후 7 01 45" src="https://user-images.githubusercontent.com/44339530/163670775-a945b754-b486-424e-b227-67864fb41037.png">

실제로 테스트해본 결과 다음과 같이 루트 `node_modules`에 위젯 디렉토리가 생성된 것을 확인할 수있다. `construction-map-client-web` 패키지에선 심링크로 이를 가져와 사용할 수 있을것이다.

#### 출처
- [https://medium.com/@deptno/monorepo-yarn-workspace-e81e3e078100](https://medium.com/@deptno/monorepo-yarn-workspace-e81e3e078100)
- [https://medium.com/wantedjobs/lerna%EC%99%80-yarn-workspaces%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-%ED%8C%A8%ED%82%A4%EC%A7%80-%EA%B4%80%EB%A6%AC-429d2a685486](https://medium.com/wantedjobs/lerna%EC%99%80-yarn-workspaces%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-%ED%8C%A8%ED%82%A4%EC%A7%80-%EA%B4%80%EB%A6%AC-429d2a685486)
- [https://velog.io/@inhyejeong59/yarn-yarn-workspace%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-monorepo-%EA%B5%AC%EC%84%B1](https://velog.io/@inhyejeong59/yarn-yarn-workspace%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-monorepo-%EA%B5%AC%EC%84%B1)
- [https://velog.io/@proshy/yarn-workspace%EB%9E%80](https://velog.io/@proshy/yarn-workspace%EB%9E%80)
- [https://musma.github.io/2019/04/02/yarn-workspaces.html](https://musma.github.io/2019/04/02/yarn-workspaces.html)
- [https://velog.io/@sms8377/DevOps-MonoRepo%EB%9E%80](https://velog.io/@sms8377/DevOps-MonoRepo%EB%9E%80)