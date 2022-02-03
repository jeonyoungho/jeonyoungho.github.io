---
title: "[1장. 리액트 입문] 27. 리액트 개발 할 때 사용하면 편리한 도구들 - Prettier, ESLint, Snippet" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-02-02 +0800
categories: [ReactWithVelopert, Chapter1] # categories는 최대 2개까지 가능
tags: [frontend, react, velopert] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

본 포스팅은 [벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)를 학습 후 정리하는 목적으로 작성하는 포스팅입니다.

# 27. 리액트 개발 할 때 사용하면 편리한 도구들 - Prettier, ESLint, Snippet

이번에는 리액트 개발을 할 때 사용하면 편리한 도구들에 대하여 알아보도록 하겠다. 이번에 다루게 되는 도구들은 모두 VS Code 와 연동해서 사용하는 도구들인데, 이 도구들은 VS Code 만 지원 하는 것이 아니라 WebStorm, Atom 등의 에디터에서도 사용이 가능하니, 만약 VS Code 가 아닌 에디터를 사용하고 계신 경우 따로 검색해서 적용해보길 바란다.

## Prettier

[Prettier](https://prettier.io/) 는 자동으로 코드의 스타일을 관리해주는 도구이다. 가령, 문자열을 사용 할 때 ' 를 쓸지 " 를 쓸지, 또는 세미콜론 (;) 를 코드 뒤에 붙일지 말지, 들여쓰기는 얼마나 할지, 이런 것들을 관리해줄 수 있다. 이 도구는 CLI 를 통해 명령어를 입력하여 사용 할 수도 있고, 다양한 에디터와 연동해서 사용 할 수도 있다.

![ePTIDs2](https://user-images.githubusercontent.com/44339530/152283022-6631db31-d25c-4447-a2a3-dcfbfbe65fa9.png)

이 도구의 특징은, 코드의 스타일을 마음대로 쉽게 커스터마이징 할 수 있다는 점이다.

![UTLbYtM](https://user-images.githubusercontent.com/44339530/152283036-a2e408f9-bee0-40ae-8553-e88455d9394b.png)

이 도구는 자바스크립트 뿐만 아니라, HTML, CSS 코드의 코드 스타일을 관리 할 수도 있고, React, Angular, Vue 등의 라이브러리도 지원 해주고 플러그인을 통하여 다른 언어도 관리해줄 수 있다.

가장 기본적인 사용 방법은 명령어를 사용하여 쓰는 것이고, Git을 통해 commit 할 때마다 자동으로 실행되도록 설정을 해줄 수도 있다.

포스팅 원작자가 권장하는 방법은 에디터와 연동해서 사용하는 것이다.(정말 편하다고 한다)

적용방법은 굉장히 간단한데, 일단 CRA 를 사용하여 새 프로젝트를 만들어보자.

```
$ npx create-react-app useful-tools
```

그리고 해당 디렉터리를 에디터로 열어주자.

그리고 루트 디렉터리 (최상위 디렉터리)에 .prettierrc 파일을 만들자.

- .prettierrc

```json
{
  "trailingComma": "es5",
  "tabWidth": 4,
  "semi": false,
  "singleQuote": true
}
```

이는 Prettier 의 기본 설정인데 [여기](https://prettier.io/docs/en/configuration.html)에서 확인 할 수도 있다.

위 설정은 아래와 같다.

<b>trailingComma</b>: "none", "es5", "all" 으로 설정을 할 수 있는데, 객체 또는 배열이 여러줄로 구성되어 있으면 다음과 같이 맨 마지막 줄에 쉼표를 붙여준다.

```javascript
const object = {
  a: 1,
  b: 2,
};
```

`"none"` 이면 쉼표를 붙이지 않고, `"es5"` 이면 객체, 배열을 사용하게 될 떄 쉼표를 붙이고, `"all"` 이면 함수를 사용 할 때 인자를 전달 할 때도 쉼표를 붙인다.

<b>tabWidth</b>: 들여쓰기의 크기를 정한다. 포스팅 원작자분은 2칸을 선호한다고 한다. 본인이 4칸이 좋다면 4로 설정해도 된다.

<b>semi</b>: 세미콜론 (;) 을 쓸지 말지 정한다. 포스팅 원작자분은 개인적으로 사용하는것을 선호한다고 한다. 사용하고 싶다면 <b>true</b> 로 설정하면 된다.

<b>singleQuote</b>: 문자열을 입력 할 때 <b>"</b> 를 쓸지 <b>'</b> 를 쓸지 정한다. 포스팅 원작자분은 <b>'</b> 를 사용하는것을 선호한다고 한다. 만약에 <b>"</b> 만 쓰고 싶다면 `false` 로 설정하면 된다.

이 외에도 다른 옵션들이 많은데 [여기](https://prettier.io/docs/en/options.html)서 참고 할 수 있다.

참고로 앞으로 이 설정으로 사용을 할 것이다:

- .prettierrc

```json
{
  "trailingComma": "all",
  "tabWidth": 2,
  "semi": true,
  "singleQuote": true
}
```

파일을 만들었으면, 에디터에서 Prettier 익스텐션을 설치하자.

<img width="1440" alt="스크린샷 2022-02-03 오후 2 08 05" src="https://user-images.githubusercontent.com/44339530/152284737-d29ceeae-76dc-4819-b801-245155322f26.png">

그 다음에는 `⌘ + ,` (윈도우/리눅스에서는 `Ctrl + ,`) 키를 눌러서 VS Code 환경 설정을 열은 뒤 Format On Save 검색 후 를 체크하시면 앞으로 저장 할 때마다 설정한 코딩 스타일에 따라 자동으로 코드가 변형된다.

<img width="1402" alt="스크린샷 2022-02-03 오후 2 09 45" src="https://user-images.githubusercontent.com/44339530/152284865-5a668516-e26c-44a3-af4b-9873d1a3b65b.png">

만약에 저장 할 때 마다 코드를 변형하지 않고 수동으로 해주고 싶다면 `F1` 키 또는 `⌘ + ⇧ + P` (윈도우는 `Ctrl + Shift + P`) 를 눌러서 Format Document 를 입력해보자. 그러면 이 명령을 위한 단축키도 나타날 것이다.

<img width="1440" alt="스크린샷 2022-02-03 오후 2 13 11" src="https://user-images.githubusercontent.com/44339530/152285238-9873fe74-ca30-4835-b3fc-198403c71ce9.png">

설정이 끝났다면 App.js 를 열어서 코드를 다음과 같이 수정해보고 저장을 해보자.

- App.js

```javascript
import React from 'react';
import './App.css';

function App() {
  const a = "hello"

              return (
    <div>
                      <p>와우</p>
    </div>
          );
}

export default App;
```

이렇게 코드를 엉망진창으로 작성하고 저장을 하면 다음과 같이 자동으로 코드의 스타일이 `.prettierrc` 에서 지정한대로 고쳐진다.

```javascript
import React from "react";
import "./App.css";

function App() {
  const a = "hello";

  return (
    <div>
      <p>와우</p>
    </div>
  );
}

export default App;
```

## ESLint
`ESLint` 는 자바스크립트의 문법을 확인해주는 도구이다. CRA 로 만든 프로젝트에는 이미 적용이 되어있어서 만약에 자바스크립트 실수를 하게 되면 터미널에 오류 또는 경고가 나타나게 된다. 예를 들어서 아까 수정한 코드처럼 a 라는 값을 선언 후 사용하지 않으면, 터미널에서 다음과 같은 결과물이 나타나게 된다.

```
Compiled with warnings.

./src/App.js
  Line 5:  'a' is assigned a value but never used  no-unused-vars

Search for the keywords to learn more about each warning.
To ignore, add // eslint-disable-next-line to the line before.
```

이번에는 ESLint 의 VS Code 익스텐션을 설치해보자.

<img width="1440" alt="스크린샷 2022-02-03 오후 2 17 33" src="https://user-images.githubusercontent.com/44339530/152285635-30648cc2-593a-435f-a4b5-0480c368848f.png">

이를 설치하고 나면 터미널에서만 보이던 경고가 에디터상에서도 보이게 된다.

<img width="1107" alt="스크린샷 2022-02-03 오후 2 18 37" src="https://user-images.githubusercontent.com/44339530/152285750-e997c09e-1eeb-466e-8bdf-6845e418d25d.png">

경고 정보는 에디터 가장 아래에 있는 경고 아이콘을 누르면 볼 수 있다.

VS Code 와의 연동은 `useEffect` 같은 Hook 을 사용 할 때 사용하면 굉장히 유용한데, 한번 다음 코드를 작성해보자.

- App.js

```javascript
import React, { useState, useEffect } from 'react';
import './App.css';

function App() {
  const [value, setValue] = useState('');
  useEffect(() => {
    console.log(value);
  }, []);

  return (
    <div>
      <p>와우</p>
    </div>
  );
}

export default App;
```

<img width="1238" alt="스크린샷 2022-02-03 오후 2 22 09" src="https://user-images.githubusercontent.com/44339530/152286047-27e08238-ed9c-4efb-93f9-ae53874d763c.png">

`useEffect` 에 등록한 함수에서 value 상태를 참조하는데, deps 배열에 이를 빠뜨리게 되면 이렇게 경고가 나타난다. 여기서 노란줄에 마우스를 올리고 "Quick Fix..." 메뉴를 누르고 "Update the dependencies array to be ~ "를 누르게 되면 되면 자동으로 `deps` 에 넣어야 하는 값이 포함된다.

<img width="1246" alt="스크린샷 2022-02-03 오후 2 23 41" src="https://user-images.githubusercontent.com/44339530/152286202-d61fef53-6d3d-49b6-a6dc-5569d9999949.png">

이런 작업을 코드를 저장 할 때 자동으로 처리되도록 할 수 도있는데, `⌘ + ,` (윈도우/리눅스에서는 `Ctrl + ,`) 키를 눌러서 VS Code 환경 설정을 열은 뒤, `Auto Fix on Save` 를 검색해서 이를 `on` 으로 설정해주면 된다. 그러면, 앞으로 ESLint 가 자동으로 고칠 수 있는 것들은 저장을 할 때 자동으로 고쳐준다.

<img width="1386" alt="스크린샷 2022-02-03 오후 2 26 57" src="https://user-images.githubusercontent.com/44339530/152286503-491bceda-25e0-4e49-a05a-f6438deeb0ac.png">

## ESLint 까다로운 규칙 적용
ESLint 에는 정말 다양한 규칙들이 있다. 지금은 기본적인 규칙들만 적용된 상태인데, 다양한 ESLint 설정이 되어있는 묶어서 라이브러리로 제공이 되기도 한다.

- [eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)
- [eslint-config-google](https://www.npmjs.com/package/eslint-config-google)
- [eslint-config-standard](https://www.npmjs.com/package/eslint-config-standard)

포스팅 원작자분의 경우엔 이전엔 `eslint-config-airbnb` 를 사용했었는데, 요즘은 기본 설정 외의 다른 설정은 적용하지 않고 코드를 작성하고 있다고 한다.

참고로 위 규칙들을 적용하게 되면 좀 까다로운 면이 은근히 많다. 한번, 이번에 `eslint-config-airbnb` 를 적용해보도록 하겠다.

우선 설치를 해주자.

```
yarn add eslint-config-airbnb

npm install eslint-config-airbnb
```

그 다음에 packge.json 파일을 열어서 "eslintConfig" 부분을 변경해보자.

- package.json

```json
"eslintConfig": {
  "extends": [
    "react-app",
    "airbnb"
  ]
},
```

저장하고 나면 App.js 에서 여기저기서 빨간줄이 나타나기 시작한다.

<img width="783" alt="스크린샷 2022-02-03 오후 2 31 58" src="https://user-images.githubusercontent.com/44339530/152286892-32154a2f-4287-4ca0-ba93-4c2b24a5b9dd.png">

만약에 ESLint 추가 설정을 하게 되는 경우에는 `eslint-config-prettier` 라는 것도 적용을 해야 하는데, 이를 적용해야 Prettier 에서 관리하는 스타일이 ESLint 에서 비활성화 된다.

```
$ yarn add eslint-config-prettier

$ npm install eslint-config-prettier
```

package.json 도 수정해주세요.

- package.json

```json
"eslintConfig": {
  "extends": [
    "react-app",
    "airbnb",
    "prettier"
  ]
},
```

이번에는, 규칙을 비활성화 하는 방법을 알아보도록 하겠다.

App.js 을 열으면 빨간줄이 나타나는 곳이 있을텐데요, 예를 들어서 JSX 쪽에 나타나는 빨간줄에 커서를 올려보면 다음과 같이 규칙 이름이 나타나게 될텐데

<img width="1439" alt="스크린샷 2022-02-03 오후 2 36 12" src="https://user-images.githubusercontent.com/44339530/152287219-d50fc41d-f558-409b-ba2b-49530e827bd0.png">

현재 위 규칙은 "react/jsx-filename-extension" 이라는 이름을 가진 규칙인데, 리액트 관련 파일은 .jsx 확장자로 설정해야 한다는 규칙이다. 이를 비활성화 하고 싶다면 다음과 같이 package.json 에서 "rules" 값을 설정하면 된다.

- package.json

```json
"eslintConfig": {
  "extends": [
    "react-app",
    "airbnb",
    "prettier"
  ],
  "rules": {
    "react/jsx-filename-extension": 0
  }
},
```

값을 0으로 설정해주면 규칙이 비활성화 된다.

<img width="1310" alt="스크린샷 2022-02-03 오후 2 37 47" src="https://user-images.githubusercontent.com/44339530/152287383-24553ee5-dd5c-40b2-a129-f3ca144c6fb5.png">

사용되지 않는 값의 경우에도 "no-unused-vars" 라는 이름으로 빨간줄이 그어지고 있는데, 만약에 이를 오류까지는 아니고 <b>경고 수준으로만 간주하고 싶다면 위 규칙에 대하여 값을 1로 설정해주면 된다.</b>

- package.json

```json
"eslintConfig": {
  "extends": [
    "react-app",
    "airbnb",
    "prettier"
  ],
  "rules": {
    "react/jsx-filename-extension": 0,
    "no-unused-vars": 1
  }
},
```

그러면 이렇게 경고 수준으로만 표시를 하게 된다.

<img width="947" alt="스크린샷 2022-02-03 오후 2 40 02" src="https://user-images.githubusercontent.com/44339530/152287563-9dea5d97-5e76-4e30-b245-73d55891dab5.png">

ESLint 에 airbnb / standard / google 같은 설정을 적용하게 되면 굉장히 까다롭기 때문에, 여럿이서 협업하는 프로젝트가 아니라면, 포스팅 원작자분은 개인적으로 Prettier 와 ESLint 의 기본 설정만 적용하고 개발을 진행하는 것을 추천한다고 한다.

## Snippet
Snippet 은 도구라기보단, 에디터마다 내장되어있는 기능이다. 한국어로는 "코드 조각" 이라고도 부르는데, Snippet 의 용도는 자주 사용되는 코드에 대하여 단축어를 만들어서 코드를 빠르게 생성해내는 것 이다.

VS Code 의 확장 마켓플레이스에서 React Snippet 이라고 검색해봐도 다양한 확장 프로그램들이 나오는데, 포스팅 원작자분은 그런 확장 프로그램을 쓰는 것 보다 직접 만들어서 사용하는 것을 선호한다고 한다. 왜냐하면 확장 프로그램을 통해서 스니펫을 사용하게 되면 실제로 사용하지 않는 스니펫들도 있고 마음에 들지 않는 단축어일때도 있기 때문이라고 한다.

우선, 우선적으로 해야 할 것은 VS Code 에서 .js 확장자에 대하여 언어 모드를 JavaScript React 로 설정하는 것이다. 기본 설정으로는 일반 JavaScript 로 되어있는데 이를 JavaScript React 로 바꿔주자.

![0Zdnhoz](https://user-images.githubusercontent.com/44339530/152287944-e406981c-1de9-44b7-a573-59c12a452f3f.gif)
_출처: https://react.vlpt.us/basic/27-useful-tools.html_

그 다음에는, 만들고 싶은 스니펫을 위한 샘플 코드를 생성하자. src 디렉터리에 Sample.js 파일을 만들어서 다음 코드를 작성해자.

- Sample.js

```jsx
import React from 'react';

function Sample() {
  return (
    <div>
      Hello React!
    </div>
  );
}

export default Sample;
```

정말 간단한 함수형 컴포넌트이다. 이를 스니펫으로 만들어보겠다. 우선, Sample 이라는 키워드가 있는 곳에 파일 이름이 들어가게 하고 싶기 때문에 Sample 을 ${TM_FILENAME_BASE} 로 바꿔주자. 이 값은 스니펫에서 사용 할 수 있는 변수인데 [여기](https://code.visualstudio.com/docs/editor/userdefinedsnippets) 에서 자세한 내용을 볼 수 있다.

```jsx
import React from 'react';

function ${TM_FILENAME_BASE}() {
  return (
    <div>
      Hello React!
    </div>
  );
}

export default ${TM_FILENAME_BASE};
```

그 다음, Hello React 부분을 ${1} 이라고 작성해주자. 이 부분이 나중에 스니펫을 통해 코드를 생성하게 됐을 때 텍스트 커서가 맨 처음 위치할 곳이다.

```jsx
import React from 'react';

function ${TM_FILENAME_BASE}() {
  return (
    <div>
      ${1}
    </div>
  );
}

export default ${TM_FILENAME_BASE};
```

그 다음에 이 코드를 복사해서 [Snippet Generator](https://snippet-generator.app/) 웹서비스를 열어서 좌측 코드 에디터에 붙여넣자.

<img width="1367" alt="스크린샷 2022-02-03 오후 2 58 42" src="https://user-images.githubusercontent.com/44339530/152289371-42d0d17f-fff7-498d-86a6-ad59d9a0b7b8.png">

상단에 Description 과 Tab Trigger 라는 입력창이 있는데, Description 에는 설명을, Tab Trigger 에는 단축어를 넣으면 된다. 포스팅 원작자분은 `f`unctional `c`omponent 라는 의미로, fc 라고 넣었다. 또는, react functional component 라는 의미로 rfc 라고 입력을 해도 된다. 마음대로 설정하면 된다.

그럼 우측에 코드 스니펫 코드가 생성되는데, 이를 복사하자.

그 다음에는 VS Code 에서 F1 키 또는 `⌘ + ⇧ + P` (윈도우는 `Ctrl + Shift + P`) 를 누르고 Configure Snippet 을 검색하자.

<img width="1198" alt="스크린샷 2022-02-03 오후 2 54 58" src="https://user-images.githubusercontent.com/44339530/152289063-5462b6a3-b2c8-494b-b219-3f4f1c6deffb.png">

그리고 `javascriptreact.json` 을 선택하면 snippet 을 위한 json 파일이 열리는데 거기에 방금 복사한 내용을 붙여넣으면 된다.

<img width="1120" alt="스크린샷 2022-02-03 오후 2 57 35" src="https://user-images.githubusercontent.com/44339530/152289288-90d06526-cb53-47b5-a68d-8a9a8f675e3f.png">

```json
{
	// Place your snippets for javascriptreact here. Each snippet is defined under a snippet name and has a prefix, body and 
	// description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
	// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the 
	// same ids are connected.
	// Example:
	// "Print to console": {
	// 	"prefix": "log",
	// 	"body": [
	// 		"console.log('$1');",
	// 		"$2"
	// 	],
	// 	"description": "Log output to console"
	// }

	"Functional Component": {
		"prefix": "fc",
		"body": [
		  "import React from 'react';",
		  "",
		  "function ${TM_FILENAME_BASE}() {",
		  "  return (",
		  "    <div>",
		  "      ${1}",
		  "    </div>",
		  "  );",
		  "}",
		  "",
		  "export default ${TM_FILENAME_BASE};"
		],
		"description": "Functional Component"
	}
}
```

이제 다시 Sample.js 파일을 열어서 모든 코드를 지운 후 fc 라고 입력 후 엔터를 눌러보자.

<img width="1035" alt="스크린샷 2022-02-03 오후 3 03 28" src="https://user-images.githubusercontent.com/44339530/152289833-c2049131-763d-4d5d-88fb-94846490dd1d.png">

그러면 아래와 같이 자동으로 코드가 생성된다.

<img width="769" alt="스크린샷 2022-02-03 오후 3 05 12" src="https://user-images.githubusercontent.com/44339530/152290008-3481d17e-080a-4446-9431-a06be43dc402.png">

앞으로 리액트 개발을 하면서 자주 사용되는 코드가 있다면 이렇게 스니펫을 만들어서 관리를 하게 된다면 개발 생산성을 높여줄 수 있을 것이다. 포스팅 원작자분은 마음대로 만들어서 사용할 수 있다는 점 때문에 직접 만들어서 사용 하는 것을 추천드리지만, 하나 하나 만들어서 사용하는게 귀찮다고 느껴지신다면 맘에드는 Snippet 확장 프로그램을 찾아서 사용해보는 것도 나쁘지는 않다고 한다.

