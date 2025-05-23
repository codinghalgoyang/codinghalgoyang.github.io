---
layout: single
title: "[web/programming] 타입"
categories: web/programming
---

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 요약

## 문법 - 타입

> 결론 : 자바스크립트의 경우, 런타임 때(데이터가 변수에 들어가는 시점)에 타입이 결정되기 때문에 잘못된 타입으로 인한 버그가 런타임때 발생함. 이는 에러파악이 어려울 뿐만 아니라, 방어코드 구현, 에러발생시 처리 코드 구현등 복잡한 일이 많이 생김. 타입스크립트의 경우, 컴파일 타임에 타입으로 인한 버그를 방지할 수 있어, 에러도 사전에 방지하고, 방어코드, 에러발생시 처리코드의 구현이 필요 없어짐.

자바스크립트와 타입스크립트에서 타입을 어떻게 다루는지에 대해 얘기하고자 함.

```js
// javascript
function addAge(age) {
  return age + 1;
}
let age = addAge(30); // 31
let age = addAge("30"); // '301' - 예상치 못한 값
```

```ts
// typescript
function addAge(age: number): number {
  return age + 1;
}
let age = addAge(30); // 31
let age = addAge("30"); // error
```

- 자바스크립트의 변수의 타입은 매번 데이터가 들어가는 시점에 자유롭게 바꾼다.
  - 타입이 없는건 아니나, 느슨함.
  - 런타임때 에러가 발생함.
  - 버그가 잘 발생할 수 있는 환경이며, 위와같은 상황을 막기 위해서는 if문으로 typeof로 검사하고, 에러처리 방법을 고려해야함.
- 타입스크립트의 변수의 타입은 하나의 타입만 받도록 함.
  - 타입스크립트는 런타임이 아닌 컴파일 타임에 오류를 발생시켜줌.
  - 자바스크립트의 느슨한 타입관리로 인한 문제를 컴파일 타임때 해결할 수 있음.
