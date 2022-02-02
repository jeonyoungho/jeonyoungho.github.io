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
