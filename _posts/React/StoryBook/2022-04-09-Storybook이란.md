---
title: "[React] Storybook이란" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-04-09 +0800
categories: [React, Storybook] # categories는 최대 2개까지 가능
tags: [storybook] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

실습 내용은 [깃허브 레포지토리](https://github.com/jeonyoungho/react-storybook-practice)를 통해 확인하실 수 있습니다.

# Storybook이란?
스토리북은 UI 컴포넌트 개발 도구이다. 스토리북은 실제 애플리케이션 외에 별도의 서버로 구동되어 화면 상에서 UI 컴포넌트들을 직접 눈으로 확인하며 조작(Controls)할 수 있다.

그리하여 Storybook을 활용하면 UI 컴포넌트를 내부 개발자들을 위해 쉽게 문서화할 수 있고 디자인 시스템을 개발하는데 훨씬 더 편리하게 도와준다.

스토리북(storybook)을 구성하는 기본 단위는 스토리(story)이며, 하나의 UI 컴포넌트는 보통 하나 이상의 Story를 가지게 된다. 각 Story는 해당 UI 컴포넌트가 어떻게 사용될 수 있는지를 보여주는 하나의 예시라고 볼 수 있다.

실제 현업에서 디자인 시스템에 있는 공통 컴포넌트들을 스토리북을 활용하여 문서화해놓음으로써 프론트엔드 개발자들의 생산성을 높일 수 있다.

# Storybook 간단 실습
## 1) Storybook 설치
아래 스크립트를 실행하여 storybook을 프로젝트에 설치할 수 있다.

```
npx -p @storybook/cli sb init
```

설치가 끝나면 package.json에 스크립트 두 개가 새로 생성된 것을 확인할 수 있다.

```json
{
    "storybook": "start-storybook -p 6006 -s public", // storybook 실행 스크립트
    "build-storybook": "build-storybook -s public" // storybook 빌드 스크립트
}
```

빌드 스크립트를 실행하면 storybook-static 디렉토리가 생성되고 빌드된 결과물들이 존재하게 된다.

## 2) Storybook 설정
`.storybook` 디렉토리를 열어보면 storybook 구성 파일 (`.storybook/main.js`)이 보일텐데 아래와 같이 변경해주자.

```javascript
module.exports = {
  "stories": [
    '../src/components/**/*.stories.js' // story를 인식한 경로 지정
  ],
  "addons": [ // addons 설정
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@storybook/addon-interactions",
    "@storybook/preset-create-react-app"
  ],
  "framework": "@storybook/react", // framework 설정
  "core": { // 스토리 빌드 도구 설정
    "builder": "@storybook/builder-webpack5"
  }
}
```

그리고 `preview.js` 파일 또한 보일텐데 아래와 같이 변경해주자.

```javascript
import '../src/index.css'; //👈 The app's CSS file goes here

//👇 Configures Storybook to log the actions( onArchiveTask and onPinTask ) in the UI.
export const parameters = {
  actions: { argTypesRegex: '^on[A-Z].*' },
};
```

`매개변수(parameters)`는 일반적으로 Storybook의 기능과 애드온의 동작을 제어하기 위하여 사용된다. 이를 사용하여 actions(mocked callbacks)이 처리되는 방식을 구성한 것이다.

actions은 클릭이 되었을때 Storybook UI의 actions 패널에 나타날 콜백을 생성할수 있도록 해준다. 따라서 버튼 컴포넌트를 만든다고 했을때 버튼 클릭이 성공적이었는지 테스트 UI에서 확인 할 수 있을 것이다.

## 3) 예시 React 컴포넌트 작성
`<SampleTask/>` 라는 간단한 React 컴포넌트를 하나 작성해보자. 먼저 src/components 디렉터리를 생성하고, 그 안에 SampleTask.js 파일을 생성하고, 그 파일 안에 아래 코드를 작성하자.

```javascript
import React from 'react';

export default function SampleTask({ task: { id, title, state }, onArchiveTask, onPinTask }) {
    return (
        <div className="list-item">
            <input type="text" value={title} readOnly={true} />
        </div>
    );
}
```

## 4) Story 추가
위에서 작성한 React 컴포넌트에 대한 Story를 작성해보자. 관례상 하나의 UI 컴포넌트에 대해 하나 이상의 Story를 위에서 설정했던 것 처럼 `<컴포넌트 이름>.stories.js`라는 하나의 파일 안에 추가한다.

먼저 src/components 디렉터리를 안에 `SampleTask.stories.js` 파일을 생성하고, 그 파일 안에 아래 코드를 작성하자.

```javascript
import React from 'react';
import SampleTask from "./SampleTask";

export default {
    component: SampleTask,
    title: 'SampleTask',
};

const Template = (args) => <SampleTask {...args} />;

export const Default = Template.bind({});
Default.args = {
    task: {
        id: '1',
        title: 'Test SampleTask',
        state: 'TASK_INBOX',
        updatedAt: new Date(2018, 0, 1, 9, 0),
    },
};

export const Pinned = Template.bind({});
Pinned.args = {
    task: {
        ...Default.args.task,
        state: 'TASK_PINNED',
    },
};

export const Archived = Template.bind({});
Archived.args = {
    task: {
        ...Default.args.task,
        state: 'TASK_ARCHIVED',
    },
};
```

default 임포트는 해당 UI 컴포넌트에 대한 메타 정보를 객체로 작성하는데 `title`은 Storybook 메뉴에서 해당 컴포넌트 스토리에 대한 이름을 지정할 수 있고, `component`에는 위에서 작성한 React 컴포넌트를 지정할 수 있다. 

named 임포트에는 UI 컴포넌트의 활용 예시를 나타내는 Story를 하나씩 함수 형태로 작성하였다.

## 5) StoryBook 구동
마지막으로 Storybook 구동 스크립트를 실행하면, 브라우저에서 http://localhost:6006/이 열릴 것이고 아래 이미지와 같이 독립적인 화면에 컴포넌트들이 보일 것이다.

<img width="634" alt="스크린샷 2022-04-26 오후 9 10 17" src="https://user-images.githubusercontent.com/44339530/165296959-e0bb1700-d443-460f-9982-f555b5e22eb4.png">

결국 Storybook은 프로젝트에서 개발하는 매인 애플리케이션과 별개로 따로 구동이 가능한 웹사이트인 것이다. 좌측 사이드바에 있는 메뉴 트리에서 SampleText를 선택하면, 그 하위에 3개의 Archived, Default, Pinned 이라는 Story가 보일 것이다. 각 Story를 선택하면 해당 Story가 실제 브라우저에서 어떻게 랜더링되는지 우측 패널을 통해 확인할 수 있다.

# Storybook의 추가적인 기능
storybook은 `UI 컴포넌트의 문서화를 통한 공유 및 공통 컴포넌트 관리` 라는 역할 외에도 몇 가지 더 다양한 역할을 가지고 있다.

> **Note**: 추가적인 기능에 대해선 [Storybook 튜토리얼](https://storybook.js.org/tutorials/intro-to-storybook/react/ko/simple-component/)을 통해 더 자세하게 살펴볼 수 있다.

## 1) 배포
Storybook 관리자가 만든 무료 배포 서비스인 [Chromatic](https://www.chromatic.com/)을 사용하여 무료로 배포하고 url로 손쉽게 공유할 수 있다. 이는 클라우드에서 Storybook을 안전하게 배포하고 호스팅 할 수 있게 한다. 그러기에 [깃허브 레포지토리 readme](https://github.com/jeonyoungho/react-storybook-practice)에 배포된 스토리북 url링크를 남기는 경우가 많다.

## 2) 테스트
스토리북을 활용하여 4가지 테스트를 수행할 수 있다.

- 수동 테스트: 개발자가 컴포넌트의 정확성을 직접 눈으로(수동으로) 확인하며 검증하는 테스트, 빌드시 컴포넌트의 모습이 온전한지 점검하는데 도움이 된다.
- 스냅샷 테스트: Storyshots을 사용하여 컴포넌트가 렌더링된 스토리의 마크업을 캡처해두어, 렌더링 오류와 경고를 유발하는 마크업의 변경사항을 파악하는데 도움을 준다.
- 단위 테스트: Jest를 사용하여 고정된 입력값을 주었을 때 컴포넌트의 출력 값이 동일하게 유지되는지를 확인한다. 컴포넌트의 기능적 품질을 테스트하는데 유용하다.
- <b>시각적 회귀 테스트(visual regression test)</b>: 모든 스토리의 스크린샷을 캡처하고 표면상 바뀐 부분을 커밋 간 비교하는 방식으로 테스팅을 수행한다. 이는 레이아웃, 색상, 크기, 대비와 같은 그래픽 요소를 확인하는데 탁월하다.

개발자라면 개발 프로세스를 자동화하는게 중요하다. 그러기에 github의 액션이나 bitbucket의 파이프라인을 활용하여 푸쉬 후 `시각적 회귀 테스트`를 통해 UI 변경 사항을 일일이 검토하면 좋다.(더 자세한 내용은 [여기](https://storybook.js.org/tutorials/intro-to-storybook/react/ko/test/)를 참고하자.)

## 3) 애드온
외부의 별도 플러그인이라 생각하면 이해하기 쉽다. storybook에서 제공하는 [애드온(add-on)](https://storybook.js.org/docs/react/configure/storybook-addons)을 활용하면 다양하고 강력한 추가 기능을 사용할 수 있다.

스토리북 튜토리얼을 보면 아래와 같이 기재되어 있다.

```
가능한 모든 사용 사례에 대한 애드온이 있기 때문에 애드온에 관한 모든 예시를 다룰 수는 없을 것입니다.
```

그만큼 애드온은 정말 다양한 것들이 존재하며 가장 인기 있는 애드온인 [Controls](https://storybook.js.org/docs/react/essentials/controls)를 사용하면 컴포넌트에 주입되는 props를 손쉽게 변경해가며 실시간으로 변경 내역을 확인할 수 있다.

<img width="710" alt="스크린샷 2022-04-26 오후 9 31 41" src="https://user-images.githubusercontent.com/44339530/165300536-15b736a2-2619-4cae-802e-b34f373c4914.png">

`Controls`를 통해 디자이너와 개발자가 컴포넌트에 전달되는 값을 바꾸어보며 손쉽게 컴포넌트의 변화를 확인해볼 수 있다.(코드 없이) 

> **Note**: Storybook을 새로 설치하게 되면 Controls이 포함되어 있기 떄문에 추가로 설정이 필요하지 않다.

#### 출처
- [https://www.daleseo.com/storybook/](https://www.daleseo.com/storybook/)
- [https://developer0809.tistory.com/171](https://developer0809.tistory.com/171)
- [https://storybook.js.org/tutorials/intro-to-storybook/react/ko/get-started/](https://storybook.js.org/tutorials/intro-to-storybook/react/ko/get-started/)ㅌ