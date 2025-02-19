---
title: "리액트 스터디 [컴포넌트, JSX] (항해99 사전스터디)"
date: 2025-02-07 21:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---

주어진 자료들을 쭉 보던중, 작년 초에 새로 나온 리액트 19가 많이 바뀌어서,
https://ko.react.dev/ 를 참고하며 아예 새롭게 할 예정입니다! 

이번에는 템포를 올려서 컴포넌트 (만들어보기), JSX, Props, Children, State까지 해보겠습니다

--- 

### React Component 

>앱의 재사용 가능한 UI 요소인 컴포넌트를 만들 수 있습니다.

>모든 UI는 컴포넌트입니다.

> React 컴포넌트는 마크업으로 뿌릴 수 있는 Javascript 함수입니다. 

#### 컴포넌트 빌드
예시
```typescript
export default function Profile() {
  return (
    <img
      src = "https://i.imgur.com/MK3eW3Am.jpg"
      alt = "Katherine Johnson"
    / >
  )
}

```
1. 컴포넌트 내보내기, export default 
2. 함수 정의하기, function Profile() {}  
3. 마크업 추가하기, src 및 alt속성을 가진 ```<img/>``` 태그를 반환합니다

<font color="olivegrab">주의 할 점</font>

- 컴포넌트는 대문자로 시작해야 합니다
- JSX 마크업이 return 키워드와 같은 라인에 있지 않으면 ()로 묶어야 합니다 안그러면 <font color='orange'>무시됩니다</font>


#### 컴포넌트 사용하기 
컴포넌트 정의를 했다면, 이제 다른 컴포넌트안에 중첩할 수 있습니다.

```
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}

```

Gallery는 각 Profile을 자식 으로 렌더링하는 부모 컴포넌트라고 할 수 있습니다.

주의 할 점

- 컴포넌트는 다른 컴포넌트를 렌더링할 수 있지만, 그 정의를 중첩해서는 안 됩니다.
- 자식 컴포넌트에 부모 컴포넌트의 일부 데이터가 필요한 경우, 정의 중첩 대신 props로 전달하세요. 

### JSX 
보통은 HTML, CSS, JS를 분리된 파일로 관리했는데, (로직이 JS안에서 분리되어 작동, HTML 안에서 내용이 마크업 됨)
웹이 interactive해지며, 로직이 내용을 결정하는게 많아져고, 그래서 JS가 HTML을 담당, react에서 렌더링 로직과 마크업이 같은 위치에 함께 있게 된 이유. (컴포넌트에서)

덕분에, 렌더링 로직과 버튼의 마크업이 같이 있어서, 변화가 생길 때마다, 서로 동기화 상태를 유지할 수 있습니다.

JSX는 확장된 문법으로, React와는 다른 개념이지만, 같이 사용도 됩니다. 
React 컴포넌트는 JSX를 사용해서 마크업을 나타냅니다. 

camelCase로  작성하세요.

문자열 어트리뷰트를 JSX에 전달하려면 작은따옴표나 큰따옴표로 묶어야 합니다.

```typescript
export default function Avatar() {
  return (
    <img
      className = "avatar"
      src = "https://i.imgur.com/7vQD0fPs.jpg"
      alt = "Gregorio Y. Zara"
    / >
  );
}
```
src 나 alt를 동적으로 지정하려면? 

```
export default function Avatar() {
  const avatar = 'https://i.imgur.com/7vQD0fPs.jpg';
  const description = 'Gregorio Y. Zara';
  return (
    <img
      className = "avatar"
      src = {avatar}
      alt = {description}
    />
  );
}
```
중괄호를 사용하면 마크업에서 바로 JavaScript를 사용할 수 있답니다.

```
export default function TodoList() {
  const name = 'Gregorio Y. Zara';
  return (
    <h1>{name}'s To Do List</h1>
  );
}
```
