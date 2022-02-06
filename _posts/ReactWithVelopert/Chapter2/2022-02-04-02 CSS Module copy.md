---
title: "[2장. 리액트 컴포넌트 스타일링하기] 02. CSS Module" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-02-04 +0800
categories: [ReactWithVelopert, Chapter2] # categories는 최대 2개까지 가능
tags: [frontend, react, velopert] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

본 포스팅은 [벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)를 학습 후 정리하는 목적으로 작성하는 포스팅입니다.

# 02. CSS Module
이번에는 CSS Module 이라는 기술에 대해서 알아보자. 리액트 프로젝트에서 컴포넌트를 스타일링 할 때 CSS Module 이라는 기술을 사용하면, CSS 클래스가 중첩되는 것을 완벽히 방지할 수 있다.

CRA 로 만든 프로젝트에서 CSS Module 를 사용 할 때에는, CSS 파일의 확장자를 .module.css 로 하면 되는데, 예를 들어서 다음과 같이 Box.module.css 라는 파일을 만들게 된다면

- Box.module.css

```css
.Box {
  background: black;
  color: white;
  padding: 2rem;
}
```

리액트 컴포넌트 파일에서 해당 CSS 파일을 불러올 때 CSS 파일에 선언한 클래스 이름들이 모두 고유해진다. 고유 CSS 클래스 이름이 만들어지는 과정에서는 파일 경로, 파일 이름, 클래스 이름, 해쉬값 등이 사용 될 수 있다.

예를 들어서 Box 컴포넌트를 만든다면 다음과 같이 코드를 작성하는데

- Box.js

```javascript
import React from "react";
import styles from "./Box.module.css";

function Box() {
  return <div className={styles.Box}>{styles.Box}</div>;
}

export default Box;
```

`className` 을 설정 할 때에는 `styles.Box` 이렇게 `import`로 불러온 `styles` 객체 안에 있는 값을 참조해야 합니다.

<img width="664" alt="스크린샷 2022-02-06 오후 9 45 27" src="https://user-images.githubusercontent.com/44339530/152681376-cd0d29ed-ec5b-4c49-b0a8-81717f5f9983.png">

클래스 이름에 대하여 고유한 이름들이 만들어지기 때문에, 실수로 CSS 클래스 이름이 다른 관계 없는 곳에서 사용한 CSS 클래스 이름과 중복되는 일에 대하여 걱정 할 필요가 없다.

이 기술은 다음과 같은 상황에 사용하면 유용하다.

- 레거시 프로젝트에 리액트를 도입할 때 (기존 프로젝트에 있던 CSS 클래스와 이름이 중복되어도 스타일이 꼬이지 않게 해준다.)
- CSS 클래스를 중복되지 않게 작성하기 위하여 CSS 클래스 네이밍 규칙을 만들기 귀찮을 때


리액트 컴포넌트를 위한 클래스를 작성 할 때 원작자 자주 사용하는 CSS 클래스 네이밍 규칙은 다음과 같다.

- 컴포넌트의 이름은 다른 컴포넌트랑 중복되지 않게 한다.
- 컴포넌트의 최상단 CSS 클래스는 컴포넌트의 이름과 일치시킨다. (예: .Button
- 컴포넌트 내부에서 보여지는 CSS 클래스는 CSS Selector 를 잘 활용한다. (예: .MyForm .my-input)

이런 규칙 외에도 [BEM Convention](http://getbem.com/naming/) 이란 것 도 있는데, 원작자는 리액트 컴포넌트와 사용하기엔 불편한 점이 있어서 부적합하다고 생각한다고 한다. (주관적인 의견으로)

만약 CSS 클래스 네이밍 규칙을 만들고 따르기 싫다면, CSS Module 을 사용하면 된다.

이번 튜토리얼에서는, 새로운 리액트 프로젝트를 생성해서 CSS Module 기술을 사용하여 커스텀 체크박스 컴포넌트를 만드는 방법을 배워보도록 하겠다.

우선 새로운 프로젝트를 생성해주자.

```
$ npx create-react-app styling-with-css-module
```

그리고, CSS Module 별도로 설치해야 할 라이브러리는 없다. 이 기능은 `webpack` 에서 사용하는 [css-loader](https://github.com/webpack-contrib/css-loader) 에서 지원되는데, CRA 로 만든 프로젝트에는 이미 적용이 되어있으니 바로 사용하면 된다.

프로젝트를 에디터로 열고, src 디렉터리에 components 디렉터리를 만든 후 , 그 안에 CheckBox.js 를 생성해주자. 먼저 CheckBox 컴포넌트의 틀 부터 준비해주겠다.

- components/CheckBox.js

```javascript
import React from 'react';

function CheckBox({ children, checked, ...rest }) {
  return (
    <div>
      <label>
        <input type="checkbox" checked={checked} {...rest} />
        <div>{checked ? '체크됨' : '체크 안됨'}</div>
      </label>
      <span>{children}</span>
    </div>
  );
}

export default CheckBox;
```

지금 당장은, 스타일링도 하지 않고, 체크 아이콘도 사용하지 않고 그냥 이 컴포넌트에 필요한 HTML 태그들만 미리 선언을 해주었다.

여기서 `...rest` 를 사용한 이유는, CheckBox 컴포넌트에게 전달하게 될 `name`, `onChange` 같은 값을 그대로 `input` 에게 넣어주기 위함이다.

다 만들었으면 App 컴포넌트에서 렌더링해보자.

- App.js

```javascript
import React, { useState } from 'react';

import CheckBox from './components/CheckBox';

function App() {
  const [check, setCheck] = useState(false);
  const onChange = e => {
    setCheck(e.target.checked);
  };
  return (
    <div>
      <CheckBox onChange={onChange} checked={check}>
        다음 약관에 모두 동의
      </CheckBox>
      <p>
        <b>check: </b>
        {check ? 'true' : 'false'}
      </p>
    </div>
  );
}

export default App;
```

이제 `yarn start` 명령어를 사용하여 개발 서버를 열은 뒤, 다음과 같이 체크 안됨 문구를 눌렀을 때 체크박스의 값이 잘 바뀌는지 확인해보자.

![NP3wRfp](https://user-images.githubusercontent.com/44339530/152682170-9dbceb37-8213-49f5-bac1-61012e16eaa7.gif)
_출처: https://react.vlpt.us/styling/02-css-module.html_

지금 input 이 아닌 텍스트 부분을 선택했는데도 값이 바뀌는 이유는 현재 우리가 해당 내용을 label 태그로 감싸줬기 때문입니다.

이제, 스타일링을 해보자. 스타일링을 하기 전에 `react-icons` 라는 라이브러리를 설치해주자.

```
$ yarn add react-icons

$ npm install react-icons
```

이 라이브러리를 사용하면 Font Awesome, Ionicons, Material Design Icons, 등의 아이콘들을 컴포넌트 형태로 쉽게 사용 할 수 있다. 해당 라이브러리의 [문서](https://react-icons.netlify.com/#/) 를 열어서 원하는 아이콘들을 불러와서 사용하면 되는데, [Material Design Icons](https://react-icons.github.io/react-icons/#/icons/md) 의 MdCheckBox, MdCheckBoxOutline 을 사용하겠다.

![lVvd6tL](https://user-images.githubusercontent.com/44339530/152682404-3d3522e7-ca0f-4bac-b537-72c7ea72071d.png)

CheckBox 컴포넌트를 다음과 같이 수정해보자.

- components/CheckBox.js

```javascript
import React from 'react';
import { MdCheckBox, MdCheckBoxOutlineBlank } from 'react-icons/md';

function CheckBox({ children, checked, ...rest }) {
  return (
    <div>
      <label>
        <input type="checkbox" checked={checked} {...rest} />
        <div>{checked ? <MdCheckBox /> : <MdCheckBoxOutlineBlank />}</div>
      </label>
      <span>{children}</span>
    </div>
  );
}

export default CheckBox;
```

<img width="468" alt="스크린샷 2022-02-06 오후 10 11 42" src="https://user-images.githubusercontent.com/44339530/152682499-d4f9d4b7-9c87-4052-b72d-2e860bc55f3a.png">

이렇게 수정을 해주면, 텍스트 대신 아이콘이 나타나게 될 것이다. 이제 컴포넌트를 스타일링 해보자.

CheckBox.module.css 파일을 components 디렉터리에 생성 후 다음 코드를 입력해주자.

- components/CheckBox.module.css

```css
.checkbox {
  display: flex;
  align-items: center;
}

.checkbox label {
  cursor: pointer;
}

/* 실제 input 을 숨기기 위한 코드 */
.checkbox input {
  width: 0;
  height: 0;
  position: absolute;
  opacity: 0;
}

.checkbox span {
  font-size: 1.125rem;
  font-weight: bold;
}

.icon {
  display: flex;
  align-items: center;
  /* 아이콘의 크기는 폰트 사이즈로 조정 가능 */
  font-size: 2rem;
  margin-right: 0.25rem;
  color: #adb5bd;
}

.checked {
  color: #339af0;
}
```

CSS Module 을 작성 할 때에는 CSS 클래스 이름이 다른 곳에서 사용되는 CSS 클래스 이름과 중복될 일이 없기 때문에 `.icon`, `.checkbox` 같은 짧고 흔한 이름을 사용해도 상관이 없다.

CSS 코드를 다 작성했으면 CheckBox.js 에서 사용을 해보겠다.

- components/CheckBox.js

```javascript
import React from 'react';
import { MdCheckBox, MdCheckBoxOutlineBlank } from 'react-icons/md';
import styles from './CheckBox.module.css';

function CheckBox({ children, checked, ...rest }) {
  return (
    <div className={styles.checkbox}>
      <label>
        <input type="checkbox" checked={checked} {...rest} />
        <div className={styles.icon}>
          {checked ? (
            <MdCheckBox className={styles.checked} />
          ) : (
            <MdCheckBoxOutlineBlank />
          )}
        </div>
      </label>
      <span>{children}</span>
    </div>
  );
}

export default CheckBox;
```

이제, 우리 컴포넌트의 스타일이 잘 반영됐는지 확인해보자.

![HBYIos7](https://user-images.githubusercontent.com/44339530/152682691-a6bc65f1-3336-4cb6-abc3-28dc4b18935d.gif)
_출처: https://react.vlpt.us/styling/02-css-module.html_

개발자 도구로 엘리먼트를 선택해보면 다음과 같이 고유한 클래스 이름이 만들어진 것을 확인 할 수 있다.

<img width="669" alt="스크린샷 2022-02-06 오후 10 18 40" src="https://user-images.githubusercontent.com/44339530/152682879-5cd857bf-dd11-4e97-adeb-6d8f3be3e3d6.png">

CSS Module 을 사용 할 때에는 `styles.icon` 이런 식으로 객체안에 있는 값을 조회해야 하는데, 만약 클래스 이름에 `-` 가 들어가 있다면 다음과 같이 사용해야한다: `styles['my-class']`

그리고, 만약에 여러개가 있다면 다음과 같이 작성해야한다: `${styles.one} ${styles.two}`

조건부 스타일링을 해야 한다면 더더욱 번거로울 것이다. `${styles.one} ${condition ? styles.two : ''}`

이전 섹션에서 Sass 를 배울 때 썼었던 [classnames](https://github.com/JedWatson/classnames) 라이브러리에는 [bind](https://github.com/JedWatson/classnames#alternate-bind-version-for-css-modules) 기능이 있는데, 이 기능을 사용하면 CSS Module 을 조금 더 편하게 사용 할 수 있다.

우선, 설치를 해주자.

```
$ yarn add classnames

$ npm install classnames
```

- components/CheckBox.js

```javascript
import React from 'react';
import { MdCheckBox, MdCheckBoxOutlineBlank } from 'react-icons/md';
import styles from './CheckBox.module.css';
import classNames from 'classnames/bind';

const cx = classNames.bind(styles);

function CheckBox({ children, checked, ...rest }) {
  return (
    <div className={cx('checkbox')}>
      <label>
        <input type="checkbox" checked={checked} {...rest} />
        <div className={cx('icon')}>
          {checked ? (
            <MdCheckBox className={cx('checked')} />
          ) : (
            <MdCheckBoxOutlineBlank />
          )}
        </div>
      </label>
      <span>{children}</span>
    </div>
  );
}

export default CheckBox;
```

`classnames` 의 `bind` 기능을 사용하면, CSS 클래스 이름을 지정해 줄 때 `cx('클래스이름')` 과 같은 형식으로 편하게 사용 할 수 있다. 여러개의 CSS 클래스를 사용해야하거나, 조건부 스타일링을 해야 한다면 더더욱 편할 것이다.

```javascript
cx('one', 'two')
cx('my-component', {
  condition: true
})
cx('my-component', ['another', 'classnames'])
```

## 기타 내용
참고로, CSS Module 은 Sass 에서도 사용 할 수 있다. 그냥 확장자를 `.module.scss` 로 바꿔주면 된다. 물론, 그 전에 `node-sass` 를 설치해야한다.

그리고, CSS Module 을 사용하고 있는 파일에서 <b>클래스 이름을 고유화 하지 않고 전역적 클래스이름을 사용하고 싶다면 아래처럼 작성하면 된다.</b>

```css
:global .my-global-name {

}
```

만약 Sass 를 사용한다면 다음과 같이 할 수도 있다.

```scss
:global {
  .my-global-name {

  }
}
```

반대로, CSS Module 을 사용하지 않는 곳에서 특정 클래스에서만 고유 이름을 만들어서 사용하고 싶다면 다음과 같이 할 수 있다.

```css
:local .make-this-local {

}
```

Sass 라면 아래와 같이 표현 할 수 있을 것이다.

```scss
:local {
  .make-this-local {

  }
}
```

# 정리
이번 튜토리얼에서는 CSS Module을 사용하는 방법을 배웠다. 이 기술은 레거시 프로젝트에 리액트를 도입하게 될 때, 또는 클래스 이름 짓는 규칙을 정하기 힘든 상황이거나 번거로울 때 사용하면 편하다.