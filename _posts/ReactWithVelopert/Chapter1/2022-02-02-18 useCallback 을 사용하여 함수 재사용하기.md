---
title: "[1장. 리액트 입문] 18. useCallback 을 사용하여 함수 재사용하기" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-02-02 +0800
categories: [ReactWithVelopert, Chapter1] # categories는 최대 2개까지 가능
tags: [frontend, react, velopert] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

본 포스팅은 [벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)를 학습 후 정리하는 목적으로 작성하는 포스팅입니다.

# 18. useCallback 을 사용하여 함수 재사용하기
`useCallback` 은 `useMemo` 와 비슷한 Hook 이다.

`useMemo` 는 특정 결과값을 재사용 할 때 사용하는 반면, `useCallback` 은 특정 함수를 새로 만들지 않고 재사용하고 싶을때 사용한다.

이전에 App.js 에서 구현했던 `onCreate`, `onRemove`, `onToggle` 함수를 확인해보자.

```javascript
const onCreate = () => {
  const user = {
    id: nextId.current,
    username,
    email
  };
  setUsers(users.concat(user));

  setInputs({
    username: '',
    email: ''
  });
  nextId.current += 1;
};

const onRemove = id => {
  // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
  // = user.id 가 id 인 것을 제거함
  setUsers(users.filter(user => user.id !== id));
};
const onToggle = id => {
  setUsers(
    users.map(user =>
      user.id === id ? { ...user, active: !user.active } : user
    )
  );
};
```

<b>이 함수들은 컴포넌트가 리렌더링 될 때 마다 새로 만들어진다. 함수를 선언하는 것 자체는 사실 메모리도, CPU 도 리소스를 많이 차지 하는 작업은 아니기 때문에 함수를 새로 선언한다고 해서 그 자체 만으로 큰 부하가 생길일은 없지만, 한번 만든 함수를 필요할때만 새로 만들고 재사용하는 것은 여전히 중요하다.</b>

<b>그 이유는, 우리가 나중에 컴포넌트에서 props 가 바뀌지 않았으면 Virtual DOM 에 새로 렌더링하는 것 조차 하지 않고 컴포넌트의 결과물을 재사용 하는 최적화 작업을 할건데, 이 작업을 하려면, 함수를 재사용하는것이 필수이다.</b>

`useCallback` 은 다음과 같이 사용한다.

```javascript
App.js
import React, { useRef, useState, useMemo, useCallback } from 'react';
import UserList from './UserList';
import CreateUser from './CreateUser';

function countActiveUsers(users) {
  console.log('활성 사용자 수를 세는중...');
  return users.filter(user => user.active).length;
}

function App() {
  const [inputs, setInputs] = useState({
    username: '',
    email: ''
  });
  const { username, email } = inputs;
  const onChange = useCallback(
    e => {
      const { name, value } = e.target;
      setInputs({
        ...inputs,
        [name]: value
      });
    },
    [inputs]
  );
  const [users, setUsers] = useState([
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com',
      active: false
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com',
      active: false
    }
  ]);

  const nextId = useRef(4);
  const onCreate = useCallback(() => {
    const user = {
      id: nextId.current,
      username,
      email
    };
    setUsers(users.concat(user));

    setInputs({
      username: '',
      email: ''
    });
    nextId.current += 1;
  }, [users, username, email]);

  const onRemove = useCallback(
    id => {
      // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
      // = user.id 가 id 인 것을 제거함
      setUsers(users.filter(user => user.id !== id));
    },
    [users]
  );
  const onToggle = useCallback(
    id => {
      setUsers(
        users.map(user =>
          user.id === id ? { ...user, active: !user.active } : user
        )
      );
    },
    [users]
  );
  const count = useMemo(() => countActiveUsers(users), [users]);
  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} onRemove={onRemove} onToggle={onToggle} />
      <div>활성사용자 수 : {count}</div>
    </>
  );
}

export default App;
```

<b>주의 하실 점은, 함수 안에서 사용하는 상태 혹은 props 가 있다면 꼭, deps 배열안에 포함시켜야 된다는 것 이다. 만약에 deps 배열 안에 함수에서 사용하는 값을 넣지 않게 된다면, 함수 내에서 해당 값들을 참조할때 가장 최신 값을 참조 할 것이라고 보장 할 수 없다. props 로 받아온 함수가 있다면, 이 또한 deps 에 넣어주어야 한다.</b>

<b>사실, useCallback 은 useMemo 를 기반으로 만들어졌다. 다만, 함수를 위해서 사용 할 때 더욱 편하게 해준 것 뿐이다. 이런식으로도 표현 할 수 있다.</b>

```javascript
const onToggle = useMemo(
  () => () => {
    /* ... */
  },
  [users]
);
```

`useCallback` 을 사용 함으로써, 바로 이뤄낼수 있는 눈에 띄는 최적화는 없다. 다음 포스팅에서, 컴포넌트 렌더링 최적화 작업을 해주어야만 성능이 최적화되는데, 그 전에, 어떤 컴포넌트가 렌더링되고 있는지 확인하기 위해서 React DevTools 라는 것을 소개드리겠다.

우선, 구글에 React DevTools 를 검색해서 크롬 웹스토어에 들어간뒤, 크롬 확장 프로그램을 설치해줘야 한다. [링크](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=ko)

설치를 하고 나면 다음과 같이 React 탭이 개발자 도구에 뜬다. 톱니바퀴 아이콘을 누르고, 'Highlight Updates' 를 체크해주자.

<img width="680" alt="스크린샷 2022-02-02 오후 2 07 27" src="https://user-images.githubusercontent.com/44339530/152096328-952a3ec9-119c-4e8c-a626-00befa5e9872.png">

이 속성을 키면 다음과 같이 리렌더링 되는 컴포넌트에 사각형 형태로 하이라이트되어 보여지게 된다.

![ovkce71](https://user-images.githubusercontent.com/44339530/152096436-abf2e1c9-8fc3-43b0-b56a-b9b0d5d892a6.gif)

지금 보면, input 이 바뀔 때에도 UserList 컴포넌트가 리렌더링이 되고 있는데 다음 포스팅에서 이 렌더링을 막아보도록 하겠다.
