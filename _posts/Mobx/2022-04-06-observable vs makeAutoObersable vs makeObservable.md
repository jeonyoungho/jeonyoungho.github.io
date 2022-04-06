---
title: "[Mobx] observable vs makeAutoObersable vs makeObservable" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-04-06 +0800
categories: [Mobx] # categories는 최대 2개까지 가능
tags: [mobx] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# Observable State
mobx에서 `observable` 을 만드는 방법은 세 가지가 있다.


1. makeObservable
2. makeAutoObservable
3. observable

## makeObservable
makeObservable을 사용하여 하나씩 notation하는 방법

```javascript
import { makeObservable, observable, computed, action } from "mobx"

class Doubler {
    value // observable

    constructor(value) {
        makeObservable(this, {
            value: observable,
            double: computed,
            increment: action,
            fetch: flow
        })
        this.value = value
    }

    get double() { // copmuted
        return this.value * 2
    }

    increment() { // action
        this.value++
    }

    *fetch() { // flow
        const response = yield fetch("/api/value")
        this.value = response.json()
    }
}
```

## makeAutoObservable

`makeAutoObservable`은 모든 프로퍼티들을 추론하여 `action`, `computed`, `observable` 등을 정한다.

mobx 공식 레퍼런스에 따르면 추론 규칙은 다음과 같다.

- 모든 자신의 속성이 observable됩니다.
- 모든 getter가 computed됩니다.
- 모든 setter가 action됩니다.
- 프로토타입의 모든 기능 은 autoAction.
- 프로토타입의 모든 생성기 함수 는 flow. (일부 변환기 구성에서는 생성기 기능을 감지할 수 없습니다. 흐름이 ​​예상대로 작동하지 않으면 flow명시적으로 지정해야 합니다.)
- false인수 에 로 표시된 멤버는 overrides주석 처리되지 않습니다. 예를 들어 식별자와 같은 읽기 전용 필드에 사용합니다.

makeAutoObservable을 사용하면, 코드가 더 짧아질 수 있다. 또한, 새로운 멤버가 추가되어도, makeObservable에 추가하지 않아도 되기 때문에, 관리하기도 쉽다.

아래 예제는 함수형으로 makeAutoObservable을 사용했지만, 클래스에서도 사용할 수 있다.
<b>단, makeAutoObservable은 super를 갖고 있거나(상속받은 경우), subclass를 갖고 있는 경우(상속하는 경우)에는 사용할 수 없다.</b>

```javascript
import { makeAutoObservable } from "mobx"

function createDoubler(value) {
    return makeAutoObservable({
        value,
        get double() {
            return this.value * 2
        },
        increment() {
            this.value++
        }
    })
}
```

## observable
observable 메서드를 사용하면, 전체 object를 한 번에 observable로 만들어준다. `observable이 되는 대상은 복제된 다음`, 그 멤버들이 전부 observable이 된다.

<b>observable이 리턴하게 되는 object는 Proxy가 된다. Proxy가 된다는 말은, 나중에 그 object에 추가되는 프로퍼티들 또한 observable이 된다는 뜻이다.</b> 그러기에 공식 문서에서도 make(Auto)Observable 사용을 더 권장하고 있다.

observable 메서드는 배열, Maps, Sets와 같은 `collection type과 함께 호출될 수 있다.`

<b>makeObservable과는 다르게, observable 메서드는 객체에 새로운 필드를 추가하거나 삭제하는것을 지원한다.</b>

```javascript
import { observable } from "mobx"

const todosById = observable({
    "TODO-123": {
        title: "find a decent task management system",
        done: false
    }
})

todosById["TODO-456"] = {
    title: "close all tickets older than two weeks",
    done: true
}

const tags = observable(["high prio", "medium prio", "low prio"])
tags.push("prio: for fun")
```

#### 출처
- [https://velog.io/@yejineee/MobX%EC%97%90%EC%84%9C-observable-action-%EB%A7%8C%EB%93%A4%EA%B8%B0](https://velog.io/@yejineee/MobX%EC%97%90%EC%84%9C-observable-action-%EB%A7%8C%EB%93%A4%EA%B8%B0)
- [https://mobx.js.org/observable-state.html#makeautoobservable](https://mobx.js.org/observable-state.html#makeautoobservable)