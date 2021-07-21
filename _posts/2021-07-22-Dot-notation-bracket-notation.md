---
layout: post
title: 점 표기법(Dot notation) vs 괄호 표기법(Bracket notation)
date: 2021-07-22 00:57:00 +0900
categories: [coding]
tag: [javascript, es6]
toc: false
comments: true
image:
  src: /assets/img/dot-vs-bracket.png
  alt: dot versus bracket notation image
---

 

 자바스크립트는 객체의 속성을 추가하는 방법으로 두 가지 점 표기법(dot)과 괄호 표기법(bracket) 두 가지가 있다. 두 방법 다 다른 언어에서 접해봤기 때문에 적당히 이해하면서 쓰고 있다가, 문득 차이가 궁금해 알아보고 글을 남긴다. 

**V8 엔진을 기준으로 합니다**



결론부터 두 표기법의 차이

* 점 표기법은 점 뒤에 오는 변수명(속성명)으로 호출한다.
* 괄호 표기법은 괄호 안의 값으로 변수명(속성명)을 호출한다.



표기법을 구분하기 전에 속성의 종류를 알아야한다. 무슨 말인지 모르겠다면 아래 예시를 읽어보자.



### 속성명은 변수명으로 저장

```javascript
let obj1 = {
    "hello": "world",
    hello: "es6"
}
let obj2 = {
    hello: "es6",
    "hello": "world"
}

console.log(obj1);  // { hello: 'es6' }
console.log(obj2);  // { hello: 'world' }
```

위에서 obj1, obj2 객체는 `"hello"` 와 `hello`  속성을 갖도록 선언했다. 차이는 속성명에 괄호 유무다. 하지만 출력해보면 실제로 두 obj1, obj2 객체에 저장된 속성은 괄호 없는 `hello`의 속성만 나온다. `hello` 속성 값은 마지막에 선언한 문자열 값이 출력된다. 속성명을 달리 하더라도 나중에 선언한 속성값에 덧씌워진 것이다. 이것으로 **문자열로 선언한 속성명은 변수명으로 저장된다**는 사실을 알았다.



 그렇다면 변수명 규칙(첫글자는 공백이나 숫자가 오면 안 됨)을 따르지 않는 문자열 속성명은 어떻게 될까?

### 속성명이 변수명으로 될 수 없으면 문자열로 저장

```js
let obj = {
    "123hello": "123world",
    " craft": "star",
    "-crfat": "star"
}

console.log(obj);
// { "123hello": "world", " craft": "start", "-craft": "star"}
```

변수명은 알파벳 문자와 언더바(_)으로만 시작할 수 있다. 이를 따르지 않는 문자열은 문자열 그대로 속성값이 된다.



### 숫자 속성값은?

```js
let obj = {
    0: 0,
    1: 10,
    2: 20
}

console.log(obj);  // { "0": 0, "1", 10, "2": 20 }
```

숫자 역시 변수명이 될 수 없기 때문에 문자열로써 저장된다.



**객체의 속성은 변수명으로 저장되거나 문자열로 저장된다는 것을 알았다.**

 이제 본론이다. 언제 점 표기법을 쓰고, 괄호 표기법을 써야할까?



**점 표기법의 속성 접근 방법은 점 뒤에 속성명을 써야한다는 것이다.**

```js
let obj = {
    hello: "world",
    " craft": "star",
    0: 10
};

console.log(obj);
// { hello: "world", " craft": "star", "0": 10 }

console.log(obj.hello);     // ok
console.log(obj." craft");  // syntax error
console.log(obj.0);         // syntax error
```

obj 객체의 속성에 접근하려면 점 뒤에 속성명을 써야하지만, 속성명 `" craft"` 와 `0`은 점으로 접근할 수 없다.



**변수명이 아닌 문자열 속성은 괄호 표기법으로 접근한다.**

```js
console.log(obj[" craft"]); // "start"
let str = " craft"
console.log(obj[str]);      // "start"
console.log(obj["0"]);      // 10

console.log(obj[0]);        // 10      -> 아니? (*)
console.log(obj["hello"]);  // "world" -> 아니? (**)
```

괄호 표기법은 문자열 값으로 속성에 접근하는 것이기 때문에, 변수에 값을 저장하고 괄호에 넣어도 된다.

 근데 여기서 헛갈리게 숫자로 된 문자열 속성은 문자열로 저장되어 있으면서 문자열 값으로 호출하나 숫자 값으로 호출하나 똑같이 작동한다. 속성명으로만 봤을때 `"0"`과 `0`을 동일 판단한다. 그리고 변수명으로 저장된 속성도 문자열 값로 호출 할 수 있다.

 [JSON 표준](https://www.json.org/json-en.html)에서 속성은 문자열로 정의하고 있다. 하지만 자바스크립트 엔진에서 속성명이 변수명이 될 수 있을 경우 점 표기법으로, 속성명이 숫자가 될 수 있을 경우 괄호 표기법을 통해 접근할 수 있게 한 것이다.



```js
JSON.stringify(object)
// 위 함수를 실행하면 객체 속성이 전부 문자열을 의미하는 쌍따옴표(")에 둘러쌓여있는 것을 알 수 있다.
```



결론

* 점 표기법은 점 뒤에 오는 변수명(속성명)으로 호출한다.
  * 속성명이 변수명 규칙에 따라 정의되어 있으면 점으로 접근 가능
* 괄호 표기법은 괄호 안의 값으로 변수명(속성명)을 호출한다.
  * 모든 속성은 괄호 표기법으로 접근 가능
