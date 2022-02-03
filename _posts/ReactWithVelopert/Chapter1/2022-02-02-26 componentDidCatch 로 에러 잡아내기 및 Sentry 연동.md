---
title: "[1장. 리액트 입문] 26. componentDidCatch 로 에러 잡아내기 / Sentry 연동" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-02-02 +0800
categories: [ReactWithVelopert, Chapter1] # categories는 최대 2개까지 가능
tags: [frontend, react, velopert] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

본 포스팅은 [벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)를 학습 후 정리하는 목적으로 작성하는 포스팅입니다.

# 26. componentDidCatch 로 에러 잡아내기 / Sentry 연동
이번에는, `componentDidCatch` 라는 생명주기 메서드를 사용하여 리액트 애플리케이션에서 발생하는 에러를 처리하는 방법을 알아보자.

먼저, 이번 튜토리얼을 진행 하기 위하여 새로운 프로젝트를 만들어주자.

```
$ npx create-react-app error-catch
```

그 다음에, 해당 디렉터리를 에디터로 열고, 개발 서버를 시작해주세요.

```
$ cd error-catch
$ yarn start
```

## 리액트 앱에서 에러가 발생하는 상황
리액트 앱이 어떤 상황에서 에러가 발생하게 되는지 알아보자.

우선, User.js 라는 파일을 src 디렉터리에 생성하여 다음과 같이 컴포넌트 코드를 작성해보자.

- User.js

```javascript
import React from 'react';

function User({ user }) {
  return (
    <div>
      <div>
        <b>ID</b>: {user.id}
      </div>
      <div>
        <b>Username:</b> {user.username}
      </div>
    </div>
  );
}

export default User;
```

이 컴포넌트는 user 라는 props 를 받아와서 해당 데이터의 id 와 username 값을 보여준다.

이 컴포넌트를 한번 App 컴포넌트에서 사용해보자.

- App.js

```javascript
import React from 'react';
import User from './User';

function App() {
  const user = {
    id: 1,
    username: 'velopert'
  };
  return <User user={user} />;
}

export default App;
```

<img width="438" alt="스크린샷 2022-02-02 오후 11 24 58" src="https://user-images.githubusercontent.com/44339530/152172536-f2871ea1-421a-4f29-a6b7-0de242619c25.png">

이런 화면이 나타날 것이다. 하지만, 만약에 user props 를 제대로 설정하지 않았다면 어떻게 될까

- App.js

```javascript
import React from 'react';
import User from './User';

function App() {
  const user = {
    id: 1,
    username: 'velopert'
  };
  return <User />;
}

export default App;
```

![iGioIFG](https://user-images.githubusercontent.com/44339530/152173213-e40f4332-9a98-4380-aae4-65296f70bb88.png)
_출처: https://react.vlpt.us/basic/26-componentDidCatch-and-sentry.html_

이렇게 에러가 발생하게 된다. 이 화면은 개발환경에서만 보여지는 에러화면인데, 우측 상단의 X 버튼을 눌러보면 실제 환경에서 보여지는 화면이 나타나게 된다.

<img width="651" alt="스크린샷 2022-02-02 오후 11 26 53" src="https://user-images.githubusercontent.com/44339530/152172912-57181ae7-30b3-4b46-b763-f96fc765a3f8.png">

실제 환경에서는 아무것도 렌더링되지 않고 흰 페이지만 나타나게 된다. 만약에 실제 서비스에서 사용자가 이러한 상황을 겪게 된다면 상당히 당황스러울 것이다.

이번 튜토리얼에서는 이런 상황에 이렇게 흰 화면을 보여주는 대신에, 에러가 발생했다는 것을 알려주는 방법에 대하여 알아볼 것 이다.

이에 대하여 진행하기 전에 어떤 상황에 또 이런 에러가 발생하는지 알아보고, 에러를 방지 할 수 있는 방법에 대해서도 알아보겠다.

일단, 방금과 같은 에러를 방지하려면 User 컴포넌트에서 다음과 같은 작업을 하면 된다.

- User.js

```javascript
import React from 'react';

function User({ user }) {
  if (!user) {
    return null;
  }

  return (
    <div>
      <div>
        <b>ID</b>: {user.id}
      </div>
      <div>
        <b>Username:</b> {user.username}
      </div>
    </div>
  );
}

export default User;
```

이렇게 하면 user 값이 존재하지 않는다면 null 을 렌더링하게 된다. 리액트 컴포넌트에서 null 을 렌더링하게되면 아무것도 나타나지 않게 된다. 이를 `"null checking"` 이라 부른다.

코드를 이렇게 작성해주면, 화면에 아무것도 보여지지 않는것은 마찬가지이지만, 적어도 에러는 발생하지 않는다. 보통 데이터를 네트워크 요청을 통하여 나중에 데이터를 받아오게 되는 상황이 발생하는 경우 이렇게 데이터가 없으면 null 을 보여주거나, 아니면 `<div>로딩중</div>` 과 같은 결과물을 렌더링하면 된다.

에러가 발생 할 수 있는 또 다른 상황에 대해서 알아보자.

```javascript
function Users({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.username}</li>
      ))}
    </ul>
  );
}
```

만약에 위와 같은 컴포넌트에 `users` 값을 설정해주지 않았을 때에도 렌더링 과정에서 오류가 발생하게 된다. `users` 가 `undefined` 이면 당연히 배열의 내장함수 map 또한 존재하지 않기 때문이다.

때문에 다음과 같이 `users` 가 없으면 다른 결과물을 반환하는 작업을 해주어야 한다.

```javascript
function Users({ users }) {
  if (!users) return null;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.username}</li>
      ))}
    </ul>
  );
}
```

또 다른 상황으로는 다음과 같은 상황이 있다.

```javascript
function Users({ users, onToggle }) {
  if (!users) return null;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id} onClick={() => onToggle(user.id)}>
          {user.username}
        </li>
      ))}
    </ul>
  );
}
```

만약에 위 컴포넌트에 `onToggle` props 를 전달하지 않으면, 에러가 발생하게 될 것이다. 에러를 방지하기 위해선 `onToggle` 을 props 로 넣어주는 것을 까먹지 않기 위해서 다음과 같이 `defaultProps` 설정을 해주는 방법이 있다.

```javascript
function Users({ users, onToggle }) {
  if (!users) return null;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id} onClick={() => onToggle(user.id)}>
          {user.username}
        </li>
      ))}
    </ul>
  );
}

Users.defaultProps = {
  onToggle: () => {
    console.warn('onToggle is missing!');
  }
};
```

다른 솔루션으로는 `PropTypes` 라는 것을 사용하는 방법도 있다. `PropTypes` 를 사용하면 필요한 데이터를 넣지 않았을 때 개발 단계에서 경고를 볼 수 있기 때문에 실수로 props 설정을 깜빡하는 일을 방지 할 수 있다. 단, 사용법이 좀 불편하고 귀찮기 때문에 이를 사용하는 것 대신에 나중에 `TypeScript` 또는 `Flow` 를 사용해서 관리 하는 것을 권장한다. (이 강좌에서는 추후 `TypeScript` 를 사용하게 된다)

## componentDidCatch 로 에러 잡아내기
이번에는 `componentDidCatch` 생명주기 메서드를 사용하여 사전에 예외처리를 하지 않은 에러가 발생 했을 때 사용자에게 에러가 발생했다고 알려주는 화면을 보여줘보자.

우선, src 디렉터리에 ErrorBoundary 라는 컴포넌트를 만들자.

- ErrorBoundary.js

```javascript
import React, { Component } from 'react';

class ErrorBoundary extends Component {
  state = {
    error: false
  };

  componentDidCatch(error, info) {
    console.log('에러가 발생했습니다.');
    console.log({
      error,
      info
    });
    this.setState({
      error: true
    });
  }

  render() {
    if (this.state.error) {
      return <h1>에러 발생!</h1>;
    }
    return this.props.children;
  }
}

export default ErrorBoundary;
```

여기서 `componentDidCatch` 메서드에는 두개의 파라미터를 사용하게 되는데 `첫번째 파라미터는 에러의 내용`, `두번째 파라미터에서는 에러가 발생한 위치` 를 알려준다.

이 메서드에서 현재 컴포넌트 상태 `error` 를 `true` 로 설정을 해주고, `render()` 메서드에서는 만약 `this.state.error` 값이 `true` 라면 에러가 발생했다는 문구를 렌더링하도록 하고 그렇지 않다면, `this.props.children` 을 렌더링하도록 처리를 해주자.

그 다음에 App 컴포넌트에서 `<User />` 컴포넌트를 감싸주자.

- App.js

```javascript
import React from 'react';
import User from './User';
import ErrorBoundary from './ErrorBoundary';

function App() {
  const user = {
    id: 1,
    username: 'velopert'
  };
  return (
    <ErrorBoundary>
      <User />
    </ErrorBoundary>
  );
}

export default App;
```

그리고 이전에 User 컴포넌트에서 null checking 을 하는 코드를 주석처리해보자.

```javascript
import React from 'react';

function User({ user }) {
  // if (!user) {
  //   return null;
  // }

  return (
    <div>
      <div>
        <b>ID</b>: {user.id}
      </div>
      <div>
        <b>Username:</b> {user.username}
      </div>
    </div>
  );
}

export default User;
```

그리고 나서 브라우저를 열어보면 다음과 같이 아까전에 봤었던 에러 화면이 나타나겠지만,

![mNOml2Z](https://user-images.githubusercontent.com/44339530/152278808-2283b091-77ca-451e-8deb-e26bf778b2e3.png)
_출처: https://react.vlpt.us/basic/26-componentDidCatch-and-sentry.html_

우측 상단의 X 버튼을 누르고 나면 흰 화면이 아닌 "에러 발생!" 이라는 문구가 보여지게 될 것이다.

<img width="646" alt="스크린샷 2022-02-03 오후 12 58 18" src="https://user-images.githubusercontent.com/44339530/152278899-2c8e6572-47a6-4d15-b4fe-c1be2088f56f.png">

## Sentry 연동
`componentDidCatch` 를 사용해서 앱에서 에러가 발생했을 때 사용자에게 에러가 발생했음을 인지시켜줄 수 는 있지만, `componentDidCatch` 가 실제로 호출되는 일은 서비스에서 "없어야 하는게" 맞다. 만약에 우리가 놓친 에러가 있다면, 우리가 이를 알아내어 예외 처리를 해주어야 한다.

우리는 발견해내지 못했지만, 사용자가 발견하게 되는 그런 오류들이 있을 것이다. 그럴 때는 `componentDidCatch` 에서 `error` 와 `info` 값을 네트워크를 통하여 다른 곳으로 전달을 해주면 된다. 다만 이를 위해서 따로 서버를 만드는건 굉장히 번거로운 작업이다. 

굉장히 괜찮은 솔루션으로, `Sentry` 라는 상용서비스가 있다. 돈내고 쓰면 더 많은 작업을 할 수 있지만, 무료 모델로도 충분히 사용을 할 수 있으므로, 장기적으로 작업하는 프로젝트에 적용을 하는 것을 권장한다.

<img width="623" alt="스크린샷 2022-02-03 오후 1 05 26" src="https://user-images.githubusercontent.com/44339530/152279471-082d31ed-b84f-4626-a09e-5ed4822994e6.png">

[Sentry](https://sentry.io/welcome/) 에서 회원가입 및 로그인을 하고 새 프로젝트를 생성하자. 회원가입 과정에서는 팀 이름은 실제로 속해있는 곳이 없다면 아무거나 자유롭게 입력해도 된다.

<img width="594" alt="스크린샷 2022-02-03 오후 1 03 54" src="https://user-images.githubusercontent.com/44339530/152279391-ba9c427c-395b-4d56-a2c4-001065e6b21e.png">

`React` 를 선택 후 프로젝트에 이름을 작성해주자.

그러면 다음과 같이 Sentry 를 여러분의 프로젝트에 적용하는 방법이 나타난다.

<img width="1155" alt="스크린샷 2022-02-03 오후 1 07 19" src="https://user-images.githubusercontent.com/44339530/152279593-98ac9de6-70ca-4c9d-91ec-4a3fd7e5446f.png">

아까 만든 프로젝트에 한번 적용을 해보자..

프로젝트 디렉터리에서 `@sentry/react @sentry/tracing` 를 설치하자.

```
# Using yarn
yarn add @sentry/react @sentry/tracing

# Using npm
npm install --save @sentry/react @sentry/tracing
```

그리고 아까 Sentry 페이지에서 나타났던 Instruction 에 나타났던 대로 작업을 그대로 해주면 된다.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import * as Sentry from "@sentry/react";
import {BrowserTracing} from "@sentry/tracing";

Sentry.init({
    dsn: "https://ec698dc3710c4ed490069c4666c8d213@o1134044.ingest.sentry.io/6181125",
    integrations: [new BrowserTracing()],

    // Set tracesSampleRate to 1.0 to capture 100%
    // of transactions for performance monitoring.
    // We recommend adjusting this value in production
    tracesSampleRate: 1.0,
});

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```

`Sentry.init()` 을 사용 할 때 넣는 dsn 값은 프로젝트마다 다르니, 본인에 맞는 dsn 값을 넣어주자.

작업을 완료 후, 리액트 앱을 브라우저에서 새로고침을 해보자.

<img width="1440" alt="스크린샷 2022-02-03 오후 1 17 56" src="https://user-images.githubusercontent.com/44339530/152280409-a04b2b3f-e3a2-4d48-96d0-0b466555a550.png">

그러면 이렇게 실시간으로 확인할 수 있다.

<img width="1438" alt="스크린샷 2022-02-03 오후 1 20 05" src="https://user-images.githubusercontent.com/44339530/152280555-6e993169-f674-4452-9e5d-57cd892ae893.png">

그런데 여기서 끝이 아니다. 이렇게 에러가 발생 했을 때 Sentry 쪽으로 전달되는 것은 개발모드일땐 별도의 작업을 하지 않아도 잘 되지만, 나중에 프로젝트를 완성하여 실제 배포를 하게 됐을 때는 `componentDidCatch` 로 이미 에러를 잡아줬을 경우 Sentry 에게 자동으로 전달이 되지 않는다.

때문에 ErrorBoundary 에서 다음과 같이 처리를 해주어야 한다.

- ErrorBoundary.js

```javascript
import React, { Component } from 'react';
import * as Sentry from "@sentry/react";

class ErrorBoundary extends Component {
    state = {
        error: false
    };

    componentDidCatch(error, info) {
        console.log('에러가 발생했습니다.');
        console.log({
            error,
            info
        });
        this.setState({
            error: true
        });
        if (process.env.NODE_ENV === 'production') {
            Sentry.captureException(error, { extra: info });
        }
    }

    render() {
        if (this.state.error) {
            return <h1>에러 발생!</h1>;
        }
        return this.props.children;
    }
}

export default ErrorBoundary;
```

`componentDidCatch` 에서 `process.env.NODE_ENV` 값을 조회하는데, 이를 통하여 현재 환경에 개발 환경인지 프로덕션 환경인지 (production / development) 확인 할 수 있다. 개발 환경에서는 `captureException` 을 사용 할 필요가 없으므로 프로덕션에서만 이 작업을 해주었다.

### 프로덕션 환경에서 잘 작동하는지 확인하기
프로덕션 환경에서도 잘 작동하는지 확인하기 위해서는 프로젝트를 빌드해주어야 한다. 프로젝트 디렉터리에서 다음 명령어를 실행해주자.

```
$yarn build

$npm run build
```

조금 기다리면 결과물이 build 디렉터리에 나타난다. build 디렉터리에 있는 파일들을 제공하는 서버를 실행하기 위해서는 다음 명령어를 실행해주자.

```
$ npx serve ./build
```

[serve](https://www.npmjs.com/package/serve) 는 웹서버를 열어서 특정 디렉터리에 있는 파일을 제공해주는 도구이다.

http://localhost:5000/ 을 브라우저로 들어가본 뒤, Sentry 에 새로운 항목이 추가됐는지 확인해보자.

<img width="705" alt="스크린샷 2022-02-03 오후 1 33 35" src="https://user-images.githubusercontent.com/44339530/152281683-23f53bf1-2df4-4469-8382-d58fa6b24870.png">

이번에는 아까와 달리 에러가 어디서 발생했는지 상세한 정보를 알아보기 쉽지 않다.

<img width="645" alt="스크린샷 2022-02-03 오후 1 34 42" src="https://user-images.githubusercontent.com/44339530/152281775-0b71149e-b086-46ab-bb09-3de5d8004fee.png">

이는 빌드 과정에서 코드가 minify 되면서 이름이 c, Xo, Ui, qa 이런식으로 축소됐기 때문이다.

만약에 코드 위치를 제대로 파악하고 싶다면 이 [링크](https://docs.sentry.io/platforms/javascript/sourcemaps/#webpack) 를 참조하면 된다.

Sentry 에서 minified 되지 않은 이름을 보려면 Sourcemap 이란것을 사용해야 하는데, 빌드를 할 때마다 `자동으로 업로드` 되도록 설정 할 수 있고, `직접 업로드` 할 수도 있고, 만약에 `Sourcemap 파일이 공개` 되어 있다면 별도의 설정 없이 바로 minified 되지 않은 이름을 볼 수 있다.

# 정리
이번 튜토리얼에서는 `componentDidCatch` 의 사용법을 알아보고, `Sentry` 와 연동하는 방법도 알아보았다. `Sentry` 와 연동하는 작업은 필수는 아니지만, 해두면 프로젝트에서 발생 할 수 있는 버그를 관리 할 때 매우 도움이 될 것이다.