---
title: "[Javascript] var let const 차이" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-20 +0800
categories: [Frontend, Javascript] # categories는 최대 2개까지 가능
tags: [javscript, es6, var, let, const] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

ES6는 [var 키워드의 단점](https://poiemaweb.com/js-data-type-variable)을 보완하기 위해 let과 const 키워드를 도입하였다.

var let const 를 아래와 같은 세 가지 기준으로 비교할 수 있다.

- 1)변수 값의 변환
- 2)변수의 유효범위
- 3)호이스팅

## 1) 변수 값의 변환

var를 사용하면 변수 선언의 경우 할당되는 값이 유동적으로 변경될 수 있는 단점을 가지고 있다.

```javascript
var name = "Marcus";
console.log(name);

var name = "Jogeonsang";
console.log(name);

output: Marcus
output: Jogeonsang
```

name이라는 변수를 2번 선언했는데도 에러가 나오지않고 각기 다른 값이 출력되는걸 볼 수 있다.

하지만 ES6 업데이트 이후로 추가된 변수 선언 방식인 let과 const는 var와 같은 선언 방식을 막고있다.

```javascript
let name = "Marcus";
console.log(name); 

let name = "Jogeonsang";
console.log(name);
output: Identifier 'name' has already been declared
```

let을 사용했을 경우에는 name이 이미 선언되었다는 에러 메시지가 나오는걸 볼 수 있다. let만 케이스로 집어넣었지만 const도 마찬가지로 변수 재할당이 안된다는 특징을 가지고있다.

그럼 let과 const는 어떤 차이가 있을까? <b>let과 const의 차이점은 변수의 immutable여부이다.</b>

let은 변수에 재할당이 가능하지만, const는 변수 재선언, 재할당 모두 불가능하다. 예시를 살펴보자.

`let`

```javascript
// let
let testCase = 'let' // output: let
let testCase = 'let2' // output: Uncaught SyntaxError: Identifier 'testCase' has already been declared
testCase = 'let3' // output: let3
```

`const`

```javascript
const testCase = 'const' // output: const
const testCase = 'const2' // output: Uncaught SyntaxError: Identifier 'testCase' has already been declared
testCase = 'const3' // output: Uncaught TypeError:Assignment to constant variable.
```

## 2) 변수의 유효범위
var는 기본적으로 function scope를 가지게되고 let, const는 block scope를 가지게된다.

`var`

```javascript
var foo = "This is String.";
if(typeof foo === 'string'){
	var result = true;
} else {
  var result = false;
}

console.log(result);    // result : true
```

`let과 const`

```javascript
var foo = "This is String.";
if(typeof foo === 'string'){
	const result = true;
} else {
  const result = false;
}

console.log(result);    // result : result is not defined
```

## 3) 호이스팅
var, let, const 모두 호이스팅은 된다. 하지만 차이점은 존재한다. 자세한 내용은 [여기](https://jeonyoungho.github.io/posts/%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85/)를 참고하자.

#### 출처
- [https://velog.io/@marcus/2019-02-10-1702-%EC%9E%91%EC%84%B1%EB%90%A8](https://velog.io/@marcus/2019-02-10-1702-%EC%9E%91%EC%84%B1%EB%90%A8)