---
title: "[React] React Typescript 프로젝트에 eslint prettier 설정" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-03-31 +0800
categories: [Frontend, React] # categories는 최대 2개까지 가능
tags: [react, typescript, eslint, prettier] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

React(with Typescript) 프로젝트를 빌딩하면서 직접 eslintrc 파일을 설정해보며 학습했던 내용들을 한 번 정리해보려고 한다.

# ESLint와 Prettier
프로젝트가 커지고 개발자가 많아지면 javascript 코드 구조가 일관성을 유지할 수 없을 가능성이 커진다.
이러한 문제를 해결해주기 위한 도구들이 `Eslint` 와 `Prettier` 이다. 이 둘의 역할을 정리해보자면 다음과 같다.

- `Eslint`: 코드의 문법을 검사하는 린팅과 코드의 스타일을 잡아주는 포맷팅 기능을 한다.
- `Prettier`: 코드의 스타일을 잡아주는 포맷팅 기능을 한다.

> **Note**: 여기서 Eslint가 포맷팅 기능을 하는데 굳이 Prettier가 왜 필요하냐? 라는 의문점이 생길 수 있는데 Prettier가 더 강력한 포맷팅 기능을 제공하기 때문이라고 한다.

# ESLint + Prettier 디펜던시 설치 및 설정
eslint 와 prettier 연결과 설정을 위해 아래 패키지들을 설치한다. `CRA` 로 프로젝트를 생성한 경우엔 맨 아래 두 줄은 지원해주기 때문에 두 줄은 제외하고 설치하면 된다.

```
yarn add -D eslint prettier
yarn add -D @typescript-eslint/eslint-plugin @typescript-eslint/parser
yarn add -D eslint-config-airbnb
yarn add -D eslint-config-prettier eslint-plugin-prettier
yarn add -D eslint-plugin-react eslint-plugin-react-hooks
yarn add -D eslint-plugin-jsx-a11y eslint-plugin-import
```

# 패키지 설명
- `eslint` : 코드의 문법을 검사하는 린팅과 코드의 스타일을 잡아주는 포맷팅 기능
- `prettier` : 코드의 스타일을 잡아주는 포맷팅 기능
- `@typescript-eslint/eslint-plugin` : Typescript 관련 린팅규칙을 설정하는 플러그인
- `@typescript-eslint/parser` : Typescript 를 파싱하기 위해 사용
- `eslint-config-airbnb` : airbnb 코딩규칙을 사용(리액트 코딩규칙 포함)
- `eslint-config-prettier` : prettier와 충돌을 일으키는 ESLint 규칙들을 비활성화 시키는 config
- `eslint-plugin-prettier : Prettier에서 인식하는 코드상의 포맷 오류를 ESLint 오류로 출력
- `eslint-plugin-react : React에 관한 린트설정을 지원
- `eslint-plugin-react-hooks : React Hooks의 규칙을 강제하도록 하는 플러그인
- `eslint-plugin-jsx-a11y : JSX 내의 접근성 문제에 대해 즉각적인 AST 린팅 피드백을 제공
- `eslint-plugin-import : ES2015+의 import/export 구문을 지원하도록 함

# eslintrc 설정
ESLint 설정 옵션에 대해 알아보자.

### env
`env` 는 사전 정의된 전역 변수 사용을 정의한다. 자주 사용되는 설정으로는 `browser`, `node` 가 있다.

```json
"env": {
  "browser": true,
  "node": true
}
```

사전 정의된 전역변수는 [공식 문서](https://eslint.org/docs/user-guide/configuring/#specifying-environments)에서 확인할 수 있습니다.

### parser(확장)
- ESLint는 구문 분석을 위해 기본적으로 Espree 파서를 사용합니다.
`@typescript-eslint/parser` 는 Typescript의 구문 분석을 합니다.

## plugin(플러그인)
ESLint는 서드파티 플러그인 사용을 지원한다. 플러그인 패키지를 설치하고, 해당 플러그인을 plugins에 추가하여 사용할 수 있다.

```json
{
  "plugins": [
    "eslint-plugin-react"
  ]
}
```

플러그인을 추가할 때, `eslint-plugin-` 접두사는 생략이 가능하다.
예를들어, `eslint-plugin-react`는 아래처럼 선언할 수 있습니다.

```json
{
  "plugins": [
    "react"
  ]
}
```


### extends(확장)
`extends`는 추가한 플러그인에서 사용할 규칙을 설정한다.
플러그인은 일련의 규칙 집합이며, 플러그인을 추가하여도 규칙은 적용되지 않는다.
규칙을 적용하기 위해서는 추가한 플러그인 중, 사용할 규칙을 추가해주어야 적용이 된다.

예를들어,` eslint:recommended`와 `react/recommended`를 사용할 수 있다.

```json
// e.g. React
{
  "plugins": [
    "react"
  ],
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended" 
  ],
}
```

  
여기서 `eslint-plugin-` 접두사를 생략하고 `plugin:`를 사용하여 사용할 플러그인을 지정할 수 있다. `eslint:all`과 `eslint:recommended`는 `ESLint에 기본으로 제공되는 확장`이다. 
`ESLint`는 `eslint:all`를 프로덕션 용도로 사용하지 않기를 권장하고 있다.([참고](https://eslint.org/docs/user-guide/configuring/#using-eslint-all))


또, 자주 사용되는 `Typescript`는 `@typescript-eslint/eslint-plugin`를 사용할 수 있다. 이 플러그인은 접미사 `eslint-plugin` 을 생략하고 설정할 수 있다.


```json
// e.g. Typescript
{
  "parser": "@typescript-eslint/parser",
  "plugins": ["@typescript-eslint"],
  "extends": [
    "plugin:@typescript-eslint/eslint-recommended"
  ]
}
```

### rules(규칙)
ESLint에는 프로젝트에서 사용하는 규칙을 수정할 수 있다. 규칙을 변경하는 경우, 다음과 같은 방법으로 설정해야 한다.

- `"off"` 또는 0: 규칙을 사용하지 않음
- `"warn"` 또는 1: 규칙을 경고로 사용
- `"error"` 또는 2: 규칙을 오류로 사용

`규칙에 추가 옵션이 있는 경우`에는 배열 리터럴 구문을 사용하여 지정할 수 있다.

```json
{
  "rules": {
    "eqeqeq": "off",
    "curly": "error",
    "quotes": ["error", "double"]
  	"comma-style": ["error", "last"],
  }
}
```

플러그인에서 규칙을 지정할 때는 `eslint-plugin-` 를 반드시 생략해야 한다. ESLint는 내부적으로 접두사없이 이름을 사용하여 규칙을 찾는다.

#### extends와 plugins, rules 의 관계? 차이점?
근데 여기서 extends와 plugins의 차이는 무엇이고 rules 까지 이셋의 관계는 무엇일까라는 의문점이 생겼다. 

이와 관련한 내용들에 대해 더 찾아보니 `extends`는 `다른 사람이 만든 규칙을 가져와서 내 규칙에 붙여서(rule에 포함) 확장되는 것`이고, `plugin`은 `다른 사람이 만든 규칙을 가져오는 것(rule에 포함 x)`이다. 

<b>즉, extends 에 그냥 뭘 확장할건지만 써주면 자동으로 rules에 rule group이 추가되고, plugin을 쓸 때는 직접 일일이 rules에 적어줘야 되는 것이다.</b>

아님 extends와 섞어서 쓰던가 해야하는데 위에 있던 예제처럼

```json
// e.g. Typescript
{
  "parser": "@typescript-eslint/parser",
  "plugins": ["@typescript-eslint"],
  "extends": [
    "plugin:@typescript-eslint/eslint-recommended"
  ]
}
```

plugin을 가져와서 extends로 확장 후 사용할수도 있다.


### globals
선언되지 않은 전역변수를 사용하는 경우 ESLint 경고가 발생하지 않도록 사용자 전역 변수를 추가할 수 있다.

### parserOptions
- ESLint 사용을 위해 지원하려는 JavaScript 언어 옵션을 지정할 수 있습니다.
- ecmaVersion: 사용할 ECMAScript 버전을 설정
- sourceType: parser의 export 형태를 설정
- ecmaFeatures: ECMAScript의 언어 확장 기능을 설정(jsx)

### settings
ESLint 구성 파일에 설정 개체를 추가할 수 있으며, 실행될 모든 규칙에 제공된다.

### 기타

#### 인라인으로 규칙 비활성화

```javascript
// 전체 파일 규칙 경고 비활성화, 파일 맨위에 아래 블록 주석 추가
/* eslint-disable */
alert('foo');

// 경고 비활성화 블록 주석
/* eslint-disable */
alert('foo');
/* eslint-enable */

// 특정 규칙 경고 비활성화
/* eslint-disable no-alert, no-console */
alert('foo');
console.log('bar');
/* eslint-enable no-alert, no-console */
```

#### 파일 그룹에 대해서만 규칙 비활성화

```json
{
  "rules": {...},
  "overrides": [
    {
      "files": ["*-test.js","*.spec.js"],
      "rules": {
        "no-unused-expressions": "off"
      }
    }
  ]
}
```

#### 파일 디렉토리 제외
`ignorePatterns 필드` 또는 `eslintignore 파일`을 작성하여 파일 및 디렉토리를 제외하도록 지정할 수 있다.

```json
// .eslintrc 파일 ignorePatterns 설정
{
  "ignorePatterns": ["temp.js", "node_modules/"],
    "rules": {
      //...
  }
}
```

```json
//.eslintignore 파일 생성
/root/src/*.js
```

#### 대체파일 사용
`.eslintignore`를 현재 작업 디렉토리가 아닌 다른 파일을 사용하려면 `--ignore-path` 옵션을 사용하여 명령행에 파일을 지정할 수 있다.

```
eslint --ignore-path .gitignore file.js
```

#### 구성 파일 사용
`ESLint` 는 기본적으로 모든 상위 폴더에서 루트 디렉토리까지 구성파일을 찾는다.
`.eslintrc`와 `package.json` 파일이 같은 디렉토리에 있는 경우, <b>.eslintrc가 우선 순위를 갖게 되며, package.json은 사용되지 않는다.</b>
ESLint를 특정 프로젝트로 제한하는 경우, 아래 선언을 사용할 수 있다.


```json
// package.json
eslintConfig = {
  root: ture,
  ...
}

// .eslintrc.*
{ 
  root: true,
  ...
}
```

> **Note**: root의 기본 값은 true이다.

### .esliontrc.json 구성

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

#### 유용한 도구
[ESLint 데모](https://eslint.org/demo)를 사용하면 UI를 이용해 ESLint 설정을 연습해볼 수 있다.


# Prettier 설정
프로젝트 최상위 경로에 `.prettierrc` 파일을 생성하고 아래와 같이 작성한다.

```json
{
  "singleQuote": true,
  "semi": true,
  "useTabs": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 120,
  "arrowParens": "always"
}
```

각 옵션들을 정리해보면 아래와 같다.

- `singleQuote` : single 쿼테이션 사용 여부
- `semi` : 세미콜론 사용 여부
- `useTabs` : 탭 사용 여부
- `tabWidth` : 탭 너비
- `trailingComma` : 여러 줄을 사용할 때, 후행 콤마 사용 방식
- `printWidth` : 줄 바꿈 할 폭 길이
- `arrowParens` : 화살표 함수 괄호 사용 방식

> **Note**: `prettier` 옵션과 관련해서 궁금한 부분이 있다면 [prettier 공식 홈페이지](https://prettier.io/docs/en/options.html) 를 참고하자.

다음에 기회되면 `eslint` 설정의 `rules` 설정에 대하여 정리하는 시간을 가지도록 하겠다.

#### 출처
- [https://velog.io/@kmlee95/React-Typescript-eslint-prettier%EC%84%A4%EC%A0%95](https://velog.io/@kmlee95/React-Typescript-eslint-prettier%EC%84%A4%EC%A0%95)
- [https://velog.io/@xortm854/Typescript-React-Eslint-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95-2%ED%8E%B8-ESLint-Prettier-%EC%84%A4%EC%A0%95](https://velog.io/@xortm854/Typescript-React-Eslint-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95-2%ED%8E%B8-ESLint-Prettier-%EC%84%A4%EC%A0%95)
- [https://baeharam.netlify.app/posts/lint/Lint-ESLint-+-Prettier-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0](https://baeharam.netlify.app/posts/lint/Lint-ESLint-+-Prettier-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)
- [https://develoger.kr/frontend/eslint-%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9%EB%B2%95/](https://develoger.kr/frontend/eslint-%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8-%EC%82%AC%EC%9A%A9%EB%B2%95/)
- [https://velog.io/@kyusung/eslint-config-2](https://velog.io/@kyusung/eslint-config-2)
- [https://velog.io/@kyusung/eslint-prettier-config](https://velog.io/@kyusung/eslint-prettier-config)