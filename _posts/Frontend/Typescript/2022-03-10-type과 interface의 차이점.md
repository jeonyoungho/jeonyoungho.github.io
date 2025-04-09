---
title: "[Typescript] Type Aliases(type)과 Interfaces(interface)의 차이"
date: 2022-03-10 +0800
categories: [Frontend, Typescript]
tags: [frontend, typescript, type, interface]
toc: true
comments: true
---

typescript 를 사용하면서 type aliases 와 interface의 차이점에 대한 궁금증이 생겼다.

둘 다 type 을 선언할 때 사용한다는 점만 알고 있었을 뿐 둘의 차이는 정확하게 무엇일까라는 의구심이 들었다.

# Type Aliases와 Interfaces의 차이

Type aliases(`type`)와 Interfaces(`interface`)는 굉장히 비슷하고, 많은 경우에 자유롭게 선택해서 사용할 수 있다. interface의 거의 모든 기능을 type에서도 사용할 수 있지만, 가장 구별되는 점은 <b>type은 새 프로퍼티를 추가하기 위해 재선언할 수 없지만 interface는 언제나 확장 가능하다는 것이다. </b>

> **Note**: 이를 Interfaces는 `Declaration merging` 이 가능하지만, Types는 `Declaration merging` 이 불가능하다고 한다.

## 1) 타입 확장 방법이 다르다.

- Interfaces: extends 키워드를 사용
  
```typescript
interface Animal {
  name: string
}

interface Bear extends Animal {
  honey: boolean
}

const bear = getBear() 
bear.name
bear.honey
```

- Types: intersactions(&) 를 사용

```typescript
type Animal = {
  name: string
}

type Bear = Animal & { 
  honey: boolean 
}

const bear = getBear();
bear.name;
bear.honey;
```

## 2) Interface는 재선언이 가능하지만 type은 한 번 생성하면 바뀔 수 없다.

- Interface: 재선언함으로써 기존에 존재하는 interface에 새 프로퍼티를 추가할 수 있다.

```typescript
interface Window {
  title: string
}

interface Window {
  ts: TypeScriptAPI
}

const src = 'const a = "Hello World"';
window.ts.transpileModule(src, {});
```

- Type: 재선언 불가, 한 번 생성하면 바뀔 수 없다.

```typescript
type Window = {
  title: string
}

type Window = {
  ts: TypeScriptAPI
}

// Error: Duplicate identifier 'Window'.
```

## interface의 경우에는 Object type을 정의할 때만 사용할 수 있고, Prmitive type을 rename(alias)하는 용도로 사용할 수 없다.

- Interface: primitype 을 재정의 불가

```typescript
// This isn't feasible with interfaces
interface X extends string {

}
```

- type: primitype 을 재정의 가능

```typescript
// Using type we can create custom names
// for existing primitives:

type SanitizedString = string
type EvenNumber = number
```

## 정리
대부분의 경우 둘 중 무엇을 사용할지 개인적 선호에 따라 선택할 수 있고, 다른 종류의 정의가 필요할 경우 TypeScript가 알려줄 것이다. 

휴리스틱한 방식을 원한다면, type의 기능이 필요해지기 전까지는 interface를 사용하는 것이 좋다.



#### 출처
- [https://driip.me/e55aa328-0ecb-4b6a-b0be-750d1740319a](https://driip.me/e55aa328-0ecb-4b6a-b0be-750d1740319a)
- [https://joonsungum.github.io/post/2019-02-25-typescript-interface-and-type-alias/](https://joonsungum.github.io/post/2019-02-25-typescript-interface-and-type-alias/)
- [https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c](https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c)