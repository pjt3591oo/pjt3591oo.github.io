---
layout: post
title:  "javascript arrow function과 array.prototype의 조합"
date:   2017-04-04 22:40:05 +0900
categories: programming
tags: [programming javascript function arrow]
published: true
count: 125
writer: pjt3591oo
---

github를 이용하여 만들 블로그의 첫 게시글입니다.

오늘은 javascript의 **arrow function**을 좀더 간단하게 사용하는 방법에 대해서 알아보도록 하겠습니다.

**arrow function**은 `=>`의 형태로 함수를 표현하는 것을 의미 합니다.

## 함수 선언

*함수 작성방법 1* : 일반적인 function 사용
```js
let arrowFunctionTest = function(arg1) {
    console.log(arg1);
    return arg1;
}
arrowFunctionTest('test');
```

*함수 작성방법 2* : =>를 사용
```js
let arrowFunctionTest = (arg1) => {
    console.log(arg1);
    return arg1;
}
arrowFunctionTest('test');
```

2에서는 function대신에 =>를 사용하여 함수 표기를 하였습니다.

처음 보면 굉장히 어색할 수도 있지만 계속 보다보면 1번보다 2번이 더 익숙해 질 것입니다.


## callback 인자로 넘기기

자 그럼 이번에는 함수 선언이 아닌 arrow function을 callback인자로 넘겨보도록 하겠습니다.

*콜백 인자 1* : 일반적인 function 사용
```.js
let list = [1, 2, 3, 4, 5];
let newList = list.map(function(item){
    return item + 1;
});
```

*콜백 인자 2* : =>를 사용
```.js
let list = [1, 2, 3, 4, 5];
let newList = list.map((item) => {
    return item + 1;
});
```

javascript코드를 작성을 하다보면 리스트에서 값을 조작하여 새로운 리스트를 만드는 경우가 상당히 많습니다.

저는 이런 경우 코드를 아무리 잘 짜도 왠지 깔끔하지 않는것 같은 느낌이 들었습니다.

하지만 arrow function을 이용하면 좀더 깔끔한 코드 작성이 가능합니다.

---

#### **arrow function 간소화 1단계**

arrow function은 인자가 하나있을 경우 `()`가 생략이 가능 합니다.

*()생략*
```.js
let list = [1, 2, 3, 4, 5];
let newList = list.map(item => {
    return item + 1;
});
```

우선 ()가 없어지니 한결 깔끔해 보입니다.

하지만 여기서 좀더 간결하게 자꾸어 줄 수 있습니다.


#### **arrow function 간소화 2단계**

위 처럼 콜백 함수 내부의 코드가 한줄이라면 {}와 return이 생략 가능 합니다.

*{}, return생략*
```.js
let list = [1, 2, 3, 4, 5];
let newList = list.map(item => item + 1;);
```
마치 map이라는 array메소드가 간단한 하나의 함수 같이 보입니다.


#### **실행결과**

```js
console.log(newList);

---결과---
[2, 3, 4, 5, 6]
```

각각의 요소가 +1된 값이 저장이 되었습니다.


## 간소화 한 arrow function을 응용해 보자

```js
let users = ['짱구,맹구', '훈이,유리', '철수,영희'];
```

시나리오 : users라는 리스트는 각각의 요소에 n명의 유저가 `,`단위로 묶여있다. 각각의 묶여있는 단위를 리스트로 만들기

결과 : `[['짱구', '맹구'], ['훈이', '유리'], ['철수', '영희']]`

매우 쉬운 상황이다. 여러 방법이 있을 수 있지만 작성을 해보도록 하겠습니다. **arrow function**과 **array.protype.map**을 이용해서 작성을 하도록 하겠습니다.

```js
let users = users.map(item => item.split(','));
```

split을 이용하면 간단하게 작성을 할 수 있습니다.

위 시나리오는 문자열 처리를 할 때 Tokenize를 하기 위해 많이 사용되는 방법입니다.

word2vec같은 라이브러리를 사용해 보셨다면 위와같이 데이터를 만드는 것을 많이 봤을겁니다


#### *마지막으로*

`=>`와 `function`이 100% 작동 방식이 동일 하진 않습니다.

[arrow(=>)와 function의 차이점](http://blog.naver.com/pjt3591oo/220877066707)