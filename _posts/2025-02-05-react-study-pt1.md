---
title: "리액트 스터디 [소개, 문법] (항해99 사전스터디)"
date: 2025-02-05 22:00:00 +0800
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99 ]
author: <author_id>   
---

후. 3번째 그룹 스터디를 시작했습니다

물론 이제 토요일이면 🇦🇺 시드니로  출발해 석사를 시작하기에, 시간이 얼마나 있을까 싶긴합니다.

알바를 하면서 수업도 가고 과제도 하고 일도 하고 스터디도 하고 운동까지 할 생각하니 
몸 생각을 좀 더 해봐야겠습니다. 비타민 영양제같은거 진짜 안먹는데, 이젠 먹어야 버틸수 있지않을까요..🤕

어쨋든. 리액트입니다.

이젠 프론트엔드 개발자라면 필수 덕목이 되어버린 리액트.

저도 10월부터 써왔지만, 사실 문서를 너무 띄엄띄엄 본게 아닌가 싶어서 꼭 다시 한번 봐야겠다고 생각했습니다.

파트 1은 React 소개입니다!

항해99에서 제공해준 학습자료를 토대로 할 예정입니다.

(교재 돈 굳었다 ㅎㅎ 💸)

---

### 왜 리액트인가
리액트는 UI를 만드는 자바스크립트 라이브러리 입니다.

Single Page Application 기반의 프론트 프레임워크 중 하나로, component 단위의 독립적인 블록을 이용한 개발 방법을 쓴다고 합니다.

> 컴포넌트?<br/> 
> 벽돌을 쌓아서 집을 짓는다.
> 헤더, 바디, 푸터 컴포넌트를 만들어 쌓아 올리듯 개발 하는것.

MPA라는게 있었는데, 이게 무슨 interaction마다 화면이 full re-rendering이 되던거라 엄청 불편했다고 합니다
그래서 SPA가 나왓는데, 서버에 리소스를 1번 요청 후, 필요할때 데이터만 받아와, 수정해주는 방식으로 화면을 render해주는데, 이게 UX측면으로 너무 자연스러워 지죠

근데 이제 이거의 단점은 SEO, 크롤러들이 찾을 수 가 없다네요? 웹사이트가 다른 유저들한테 서치가 불가능 하다는거에요.

그래도 왜 쓰냐, 일단 대 기 업 메타가 만들고 유지보수 중이고요, 막강한 커뮤니티와 자료를 가지고 있습니다 (이게 처음엔 얼마나 중요한지 모르다가, 정말 인기없는 언어나 프레임워크 딱 한번 만 써보면 진짜 다신 ㅋㅋㅋ 안쓰게 됩니다..)

그리고 앱 개발 1등인 ReactNative와 상성이고요, VR같은 곳에도 쓰이고,
채용공고 압도적 1티어 입니다. (써야 하는 이유 최강자.)


### 자주 쓰이는 문법

const 와 let을 이용하는데
const는 재할당이 안되고
let은 재할당 가능 

### Object 객체 선언

```typescript
const customer = {
  name: '동현',
  age: 23,
  company: 'baselabs',
  doWork: () => {
  },
}
```

생략해서는 표현을
```typescript
const name = '동현';
const age = 23;

const customer = {
  name,
  age,
  company: 'baselabs',
  doWork: () => {
  },
}
```

### Template Literals
내장된 표현식을 허용하는 문자열 리터럴입니다

`` 로 표현을 하고요
```typescript
`string text`
`string text line 1
 string text line 2`
`string text ${expression} string text`
```
이런식으로 사용할 수 있습니다.

### Object destructuring 
일단 왜 destructuring 할까가 궁금했는데요, 배열이나 객체에서 필요한 값을 빠르게 추출하여 변수에 할당하는 문법이랍니다

코드를 더 간결하게 작성할 수 있고, 가독성 향상에 필요한 데이터만 변수에 저장할 수 있어서 성능에 최적화가 된답니다.

object destructuring은 객체에서 특정 property 값을 변수에 할당할 때 사용됩니다.
```typescript
const person = {
  name: '동현',
  age: '23'
}
```

많이 쓰는 패턴으로는
```typescript
function hello({name, age}) {
  console.log(`${name}님, ${age}살이시네요!`);
}
```
함수의 입력값 또한 각각 구조가 해체되어 각각 변수에 할당됩니다.

### Array destructuring
배열의 값을 변수에 할당하는 방식입니다.
```typescript
const testArr = [1, 2, 3, 4, 5];
const [val1, val2, val3, val4, val5] = testArr;

console.log(val1); // 1
```

#### 일부 요소에만 할당 
```typescript
const testArr = [1, 2, 3, 4, 5];

const [first, , third] = testArr;

console.log(first); // 1
console.log(third); // 3

```

#### 기본 값 할당

```typescript
const [name, age, region, height = 150] = ["Tom", 10, "Seoul"];

console.log(height); // 150
```

#### Object과 Array를 함께
```typescript
const users = [
  {id: 1, name: "Alice"},
  {id: 2, name: "Bob"}
];

const [{name: firstUserName}, {name: secondUserName}] = users;

console.log(firstUserName); // "Alice"
console.log(secondUserName); // "Bob"

```

#### Rest 패턴을 활용 ...rest
```typescript
const [first, second, ...rest] = [1, 2, 3, 4, 5];

console.log(first); // 1
console.log(second); // 2
console.log(rest); // [3, 4, 5]

```
... 은 다른 이름이 있는데 Spread Operator (전개 연산자 라고 합니다)

#### API 예제
```typescript
const response = {
  data: {
    user: {
      id: 1,
      name: "Brian",
      email: "testing@example.com",
      age: 23
    }
  },
  status: 200
};

const {
  data: {
    user: {name, email}
  }
} = response;

console.log(name); // "Brian"
console.log(email); // "testing@example.com"

```
