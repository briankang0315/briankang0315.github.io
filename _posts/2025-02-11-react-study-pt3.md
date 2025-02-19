---
title: "리액트 스터디 [props, state] (항해99 사전스터디)"
date: 2025-02-11 21:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---

호주 정착하고, 다시 자취 시작하고, 대학 오리엔테이션 다니면서, 헬스도 5개월만에 다시 하면서 스터디를 며칠동안 쉬었네요..

굳을 '뻔'한 머리를 다시 굴려보겠습니다

리액트가 웹 언어다 보니 너어어어어어어무 자주 업데이트가 되고, 바뀌는것 같습니다.

불과 몇개월전 꺼가 바뀌고 사라지는.. 그래서 항해99에서 제공해준 스터디 교재가 좀 deprecated된게 많아서 조금 아쉽긴 합니다.

그래도 진행해 보겠습니다.

---

## Props

컴포넌트는 props를 이용해 서로 통신합니다.

모든 부모 컴포넌트를 props를 줌으로써 몇몇의 정보를 자식 컴포넌트에게 전달할 수 있습니다.

객체, 배열, 함수를 포함한 모든 Javascript 값을 전달할 수 있습니다.

### 친숙한 props
JSX 태그에 전달하는 정보가 props입니다

예를 들어 className, src, alt, width, height는 ```<img>``` 태그에 전달할 수 있습니다.

이건 이제 이미 존재하는 태그이기에, props들이 미리 정의되어 있습니다.

### 컴포넌트에 props 전달하기
 
단계가 2개가 있습니다
1. Child component에 props 전달하기

```typescript
export default function Profile() {
  return (
    <Avatar
      person = {{ name: 'Lin Lanying', imageId: '1bX5QH6'}}
      size = {100}
    />
  );
};
```

이렇게 하면 Avatar에서 props를 읽을수 있습니다.

2. Child component 내부에서 props 읽기

```typescript
function Avatar({person, size}) {
  // person과 size는 이곳에서 사용가능합니다.
}
```

요렇게, 하면 Avatar 코드 내에서 변수를 사용하는 것처럼 사용할 수 있습니다.

props를 사용하면, child component를 독립적으로 생각할 수 있습니다.
함수의 인수와 동일한 역할을 하기에, 조절할 수 있는 손잡이라고 생각하라고 합니다.

React의 컴포넌트 함수는 하나의 인자, 즉 props 객체를 받습니다.

보통은 전체 props는 필요하지 않기에, 개별 props로 구조 분해 할당합니다.

- props 선언시 () 안에 {} 쌍을 놓치지 말고
```typescript
function Avatar({person, size}) {
  // ...
}
```

- 문법을 "구조 분해 할당"이라고 부르며 함수 매개변수의 속성과 동등합니다
```typescript
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ...
}
```
props 기본값을 할당해줄 수 있습니다. 변수 바로 뒤에 = 로.
```typescript
function Avatar({person, size=100}){
  // . ..
}
```

### Spread ... 로 props 전달
때때로 반복적인 props가 있습니다.
많은 것들도 있을 수 있기에 이런 spread 문법이 합리적일 수 있습니다. 
그래도 제한적으로 사용하세요.

```typescript
function Profile(props) {
  return (
    <div className = "card" >
      <Avatar {...props}/>
    </div>
  );
}
```

### Child를 JSX 전달하기

내장된 브라우저 태그는 중첩하는것이 일반적
```typescript
<div>
  <img />
</div>
```
(아니 근데 왜 가끔 코드가 색깔이 맘대로 바뀔까요)

때로는 같은 방식으로 자체 컴포넌트를 중첩하고 싶을 때가 있습니다.
```typescript
<Card>
    <Avatar/>
</Card>
```

이렇게 태그내에 중첩을 하면, 부모 컴포넌트는 해당 콘텐츠를 children 이라는 prop으로 받을 것 입니다.

children prop을 가지고 있는 컴포넌트는 부모 컴포넌트가 임의의 JSX로 "채울" 수 있는 "구멍"이 있는 것으로 생각할 수 있습니다.

> props는 변경할 수 없습니다. 상호작용이 필요한 경우 state를 설정해야 합니다.

## State
버튼을 만들었다고 칩시다! 

```handleClick``` 같은 event handler가 있는데, 지역 변수 index를 업데이트 하고 있습니다,

하지만 눌렀을때 아무런 변화도 일어나지 않습니다. 적어도 화면에서는요

이건 
1. 지역 변수는 렌더링 간에 유지되지 않는다
2. 지역 변수를 변경해도 렌더링을 일으키지 않습니다. 

컴포넌트를 새로운 데이터로 업데이트하기 위해선 2가지가 필요합니다

1. 렌더링 사이에 데이터 유지
2. 렌더링 유발

이걸 충족시켜주는게 바로 state입니다

### useState()
(제일 기본적인)
```useState()```를 사용해서 state를 만들어줍니다

```typescript
const [name, setName] = useState("동현");
```

useState()라는 함수는 리액트에만 존재하는 기능입니다 그리고 이건 일종의 훅 HOOK 이죠.

#### 사용법

```const [ value, setValue ] = useState(초깃값)```

const로 선언을 하고, [] 배열안에 state의 이름, set을 붙힌 state이름.
그리고 useState()안에 처음 initial value값을 넣어줍니다.

state를 변경하고 싶다면, setName(바꾸는값)을 해주면 됩니다. 
물론 UI상호작용을 통해서 되게 해야죠.

