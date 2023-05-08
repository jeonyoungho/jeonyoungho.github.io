---
title: "[Webpack] resolve alias 설정" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-04-12 +0800
categories: [Frontend, Environment] # categories는 최대 2개까지 가능
tags: [webpack] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

javascript import 키워드로 모듈을 가져올때 일반적으로는 아래와 같이 상대 경로를 통해 해당 모듈을 가져오게 된다.

```javascript
import child from './store/child';
```

하지만 만약 이를 절대 경로를 하고자 한다면 어떻게 해야할까? 바로 웹팩에 resolve object에 alias 설정을 해주면 된다. 실습을 위해 `util` 디렉토리 생성 후 util 클래스를 하나 만들어준다.

<img width="184" alt="스크린샷 2022-04-12 오후 9 26 31" src="https://user-images.githubusercontent.com/44339530/162962296-a05ec721-386c-4131-a62a-91cff3a4f6a5.png">

그리고 `webpack.config.js` 파일에 아래와 같은 설정을 추가해준다.

- webpack.config.js

```javascript
module.exports = (env = {}) => {
    ...
    resolve: {
        alias: {
            Util: path.resolve(__dirname, 'src/util'),
        },
        extensions: [".js", ".jsx", ".ts", ".tsx"], // 배열안 확장자에 따라서 처리
    },
}
```

그리고 typescript가 적용된 경우 `compilerOptions`에 다음과 같이 추가한다.

```json
{
  "compilerOptions": {
    ...
    "baseUrl": "./",
    "paths": {
      "Util/*": ["src/util/*"]
    }
  },
  "include": [
    "./src"
  ]
}

```

그러면 실제 컴포넌트에서 다음과 같이 절대 경로를 통해 모듈을 import 해올 수 있다.

```javascript
...
import util from "Util/util";

interface Props {

}

const styles = {
  padding: "50px"
};

const App: React.FC<Props> = observer((props) => {
    ...

    return (
        <div style={styles}>
            <div className={s.text}>person name: {person.name}</div>
            <div>util value is {util.value}</div>
            ...
        </div>
    );
});
export default App;
```

실행하면 다음과 같이 정상적으로 모듈을 가져와 브라우저에 출력하는 것을 확인할 수 있다.

<img width="320" alt="스크린샷 2022-04-12 오후 9 31 10" src="https://user-images.githubusercontent.com/44339530/162962875-530ac1cf-9af9-4bc0-a4f4-5e73e49faee2.png">

#### 출처
- [https://webpack.kr/configuration/resolve/](https://webpack.kr/configuration/resolve/)
- [https://webpack.kr/concepts/module-resolution](https://webpack.kr/concepts/module-resolution)
- [https://webpack.kr/configuration/resolve/#resolvemodules](https://webpack.kr/configuration/resolve/#resolvemodules)
- [https://jonghyun-park.medium.com/webpack-typescript%EC%97%90%EC%84%9C-path-alias-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-ec32428622d2](https://jonghyun-park.medium.com/webpack-typescript%EC%97%90%EC%84%9C-path-alias-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-ec32428622d2)
- [https://im-developer.tistory.com/186](https://im-developer.tistory.com/186)
- [https://suk9.tistory.com/53](https://suk9.tistory.com/53)
- [https://zereight.tistory.com/1051](https://zereight.tistory.com/1051)