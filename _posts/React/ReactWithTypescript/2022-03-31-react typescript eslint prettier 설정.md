---
title: "React Typescript 프로젝트에 eslint prettier 설정" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-03-31 +0800
categories: [Frontend, React] # categories는 최대 2개까지 가능
tags: [react, typescript, eslint, prettier] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

React(with Typescript) 프로젝트를 빌딩하면서 직접 eslintrc 파일을 설정해보았다.
대부분 많은 글들을 보면 비슷한 디펜던시를 다운 받고 어느 정도의 공통된 설정을 하는 것을 볼 수 있었다.

예를 들면 아래와 같은 디펜던시를 설치하고 eslintrc 파일 작성한다.

- eslint : 코드의 문법을 검사하는 린팅과 코드의 스타일을 잡아주는 포맷팅 기능
- prettier : 코드의 스타일을 잡아주는 포맷팅 기능
- @typescript-eslint/eslint-plugin : Typescript 관련 린팅규칙을 설정하는 플러그인
- @typescript-eslint/parser : Typescript 를 파싱하기 위해 사용
- eslint-config-airbnb : airbnb 코딩규칙을 사용(리액트 코딩규칙 포함)
- eslint-config-prettier : prettier와 충돌을 일으키는 ESLint 규칙들을 비활성화 시키는 config
- eslint-plugin-prettier : Prettier에서 인식하는 코드상의 포맷 오류를 ESLint 오류로 출력
- eslint-plugin-react : React에 관한 린트설정을 지원
- eslint-plugin-react-hooks : React Hooks의 규칙을 강제하도록 하는 플러그인
- eslint-plugin-jsx-a11y : JSX 내의 접근성 문제에 대해 즉각적인 AST 린팅 피드백을 제공
- eslint-plugin-import : ES2015+의 import/export 구문을 지원하도록 함
(출처: [https://velog.io/@kmlee95/React-Typescript-eslint-prettier%EC%84%A4%EC%A0%95](https://velog.io/@kmlee95/React-Typescript-eslint-prettier%EC%84%A4%EC%A0%95))

- eslint.rc

```javascript
module.exports = {
    env: {
        "browser": true,
        "node": true,
        es2022: true,
    },
    ignorePatterns: ["node_modules/"],
    parser: '@typescript-eslint/parser',
    plugins: ['@typescript-eslint', 'prettier', 'import'],
    extends: [
        'airbnb',
        'plugin:import/errors',
        'plugin:import/warnings',
        'plugin:prettier/recommended',
        'plugin:@typescript-eslint/recommended',
    ],
    rules: {
        '@typescript-eslint/no-var-requires': "off",
        'linebreak-style': "off",
        'import/prefer-default-export': "off",
        'prettier/prettier': "off",
        'import/extensions': "off",
        'no-use-before-define': "off",
        "@typescript-eslint/no-use-before-define": ["warn"],
        'import/no-unresolved': "off",
        "import/no-extraneous-dependencies": ["error", {"devDependencies": ["**/*.test.js", "**/*.spec.js"]}], // 테스트 또는 개발환경을 구성하는 파일에서는 devDependency 사용을 허용
        'no-shadow': "off",
        'react/prop-types': "off",
        'react/jsx-filename-extension': ["warn", { extensions: ['.js', '.jsx', '.ts', '.tsx'] }],
        'jsx-a11y/no-noninteractive-element-interactions': "warn",
        "@typescript-eslint/no-empty-interface": "warn",
        "@typescript-eslint/no-empty-function": "warn",
    },
};
```

근데 여기서 궁금증이 하나 생겼다. extends와 plugins의 차이는 무엇일까라는 궁금증이 생겼다. 

이와 관련한 내용들에 대해 더 찾아보니 `extends`는 `다른 사람이 만든 규칙을 가져와서 내 규칙에 붙여서(rule에 포함) 확장되는 것`이고, `plugin`은 `다른 사람이 만든 규칙을 가져오는 것(rule에 포함 x)`이다. 

<b>즉, extends 에 그냥 뭘 확장할건지만 써주면 자동으로 rules에 rule group이 추가되고, plugin을 쓸 때는 직접 일일이 rules에 적어줘야 되는 것이다. (아님 extends와 섞어서 쓰던가)</b>

#### 출처
- [https://velog.io/@kmlee95/React-Typescript-eslint-prettier%EC%84%A4%EC%A0%95](https://velog.io/@kmlee95/React-Typescript-eslint-prettier%EC%84%A4%EC%A0%95)
- [https://velog.io/@xortm854/Typescript-React-Eslint-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95-2%ED%8E%B8-ESLint-Prettier-%EC%84%A4%EC%A0%95](https://velog.io/@xortm854/Typescript-React-Eslint-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95-2%ED%8E%B8-ESLint-Prettier-%EC%84%A4%EC%A0%95)
- [https://baeharam.netlify.app/posts/lint/Lint-ESLint-+-Prettier-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0](https://baeharam.netlify.app/posts/lint/Lint-ESLint-+-Prettier-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)
- [https://develoger.kr/frontend/eslint-%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9%EB%B2%95/](https://develoger.kr/frontend/eslint-%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9%EB%B2%95/)