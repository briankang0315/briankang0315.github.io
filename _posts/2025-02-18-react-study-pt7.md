---
title: "리액트 스터디 [리스트 렌더링] (항해99 사전스터디)"
date: 2025-02-18 22:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---


React에서 리스트 렌더링은 배열 데이터를 기반으로 여러 컴포넌트를 효율적으로 생성하고 표시하는 방법입니다. 이를 통해 반복적인 UI 요소를 간결하게 구현할 수 있습니다.

## 배열 데이터 렌더링하기

배열의 각 요소를 컴포넌트로 변환하려면 JavaScript의 `map()` 함수를 사용합니다.

```jsx
const people = [
  { id: 1, name: 'Creola Katherine Johnson', profession: 'Mathematician' },
  { id: 2, name: 'Mario José Molina-Pasquel Henríquez', profession: 'Chemist' },
  { id: 3, name: 'Mohammad Abdus Salam', profession: 'Physicist' },
  // 추가 데이터...
];

function List() {
  const listItems = people.map(person =>
    <li key={person.id}>
      {person.name}: {person.profession}
    </li>
  );
  return <ul>{listItems}</ul>;
}
```

위 코드에서 `people` 배열의 각 객체를 `<li>` 요소로 변환하여 `<ul>` 목록으로 렌더링합니다.

## 배열 항목 필터링하기

특정 조건에 따라 배열을 필터링하려면 `filter()` 함수를 사용합니다.

```jsx
const chemists = people.filter(person => person.profession === 'Chemist');

function ChemistList() {
  const listItems = chemists.map(person =>
    <li key={person.id}>
      {person.name}
    </li>
  );
  return <ul>{listItems}</ul>;
}
```

이렇게 하면 `profession`이 'Chemist'인 사람들만 목록에 표시됩니다.

## Key를 사용하여 리스트 항목 추적하기

리스트를 렌더링할 때 각 항목에는 고유한 `key` prop을 지정해야 합니다. 이는 React가 각 항목을 고유하게 식별하여 효율적으로 업데이트할 수 있도록 도와줍니다.

```jsx
const listItems = people.map(person =>
  <li key={person.id}>
    {person.name}: {person.profession}
  </li>
);
```

`key`는 배열 내에서 각 항목을 고유하게 식별할 수 있는 값이어야 하며, 일반적으로 데이터의 고유 ID를 사용합니다.

### Key 선택 시 주의사항

- 배열 인덱스 사용 지양: 배열의 인덱스를 `key`로 사용하는 것은 권장되지 않습니다. 배열의 순서가 변경되거나 항목이 추가/삭제될 때 예기치 않은 동작이 발생할 수 있습니다.

- 고유한 값 사용: 데이터 내에서 각 항목을 고유하게 식별할 수 있는 값을 `key`로 사용해야 합니다.

## 정리

- `map()` 함수: 배열의 각 요소를 컴포넌트로 변환하여 렌더링합니다.

- `filter()` 함수: 특정 조건에 따라 배열을 필터링하여 원하는 항목만 렌더링합니다.

- `key` prop: 리스트의 각 항목에 고유한 `key`를 지정하여 React가 효율적으로 업데이트를 관리할 수 있도록 합니다.
