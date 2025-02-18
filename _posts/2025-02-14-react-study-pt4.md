---
title: "리액트 스터디 [불변성] (항해99 사전스터디)"
date: 2025-02-14 13:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---

사실 리액트를 처음하는것이 아니기에, 좀 시간도 없는데 하나하나 정리를 하는게 시간이 너무 소요가 되는것 같습니다.

앞으로 리액트한에서는 모르는것만, 제 기준 어려운것만 적어볼 예정입니다.

---

## 불변성 Immutability

> 불변성이란? 

메모리에 있는 값을 변경할 수 없는것.

원시 데이터는 불변성이 있고

객체, 배열, 함수는 없습니다.


### 메모리

변수를 저장할때, 메모리는 어떻게 될까? 

우리가 ```let number = 1```이라 선언을 하면
메모리에는 1이라는 값이 저장 됩니다.

그리고 ```number``` 라는 변수는 메모리에 있는 1을 참조하죠

그러고 ```let secondNumber = 1``` 이라고 다른 변수를 선언했다 했을때,
이미 메모리에 생성되어 있는 1이란 값을 참조합니다.

즉 number 와 secondNumber는 변수의 이름이 다르지만, 같은 메모리의 값을 보고 있는 것이죠.
그래서 콘솔에 number === secondNumber를 하면 true 가 보입니다

하지만 객체 배열 함수는, ```let obj_1 = {name: 'kim'}``` 이라는 값을 선언하면 메모리에 obj_1이 저장이 됩니다, 그리고 
```let obj_2 = {name: 'kim'}```이라고 같은 값을 선언하면 obj_2라는 메모리 공간이 새롭게 저장이 됩니다

그래서 obj_1 === obj_2 는 false가 되지요.

### 데이터 수정

기존에 1 이던 ```number```를 ```number = 2 ``` 라고 새로운 값을 할당하면, 불변성이 있는 원시 데이터기에, 메모리에 저장
되어있는 1은 그대로 있고, 새로운 저장 공간에 2가 생기고 number라는 값을 그 2으로 참조하게 합니다.

secondNumber를 콘솔에 찍으면 여전히 1이라고 보입니다.
number 와 각각 다른 메모리 저장공간을 참조하기 때문이죠

obj_1을 수정해봅시다, ```obj_1.name = 'park'```이라고 새로운 값을 할당하면, 객체에는 불변성이 없기에 기존 메모리 저장 공간에 있는
{name: 'kim'} 이라는 값이 {name: 'park'}으로 바뀌어 버립니다.

> 원시데이터는 수정했을때 메모리에 저장된 값 자체는 바꿀수 없고, 새로운 메모리 저장공간에 새로운 값을 저장해, 참조하고

> 나머지는 수정했을때 기존에 저장되어있던 메모리 저장공간의 값을 바꿔버립니다

### 왜 중요할까?

화면 렌더링은 state 변화의 따라서 입니다. 변했으면 리렌더링 하는것, 아니면 리렌더링을 하지 않죠. 

이때 변화 확인 방법이 state 변화 전후의 메모리 주소를 비교합니다.
불변성을 지켜주지 않고, 값은 바뀌지만 메모리 주소는 변함이 없게 되는것이죠.

```typescript
const [dogs, setDogs] = useState(["말티즈"]);

function onClickHandler(){
  // spread operator로 dogs를 복사하고 항목을 추가 합니다
  setDogs([...dogs, "시고르르르르잡종"]);
}
console.log(dogs);

return(
    <div> 
     <button onClick={onClickHandler}>Button</button>
    </div>
);
```

## 순수 함수

순수 함수는 입력값이 동일하면 항상 같은 결과를 반환하고, 외부 상태를 변경하지 않는 함수입니다. 함수 내부에서 부작용(Side Effects)이 없고, 동일한 인자를 넣으면 결과가 항상
같아야 합니다.

### 순수 함수 (Pure Function)

```jsx
const addSixPure = (arr) => {
  // 원본 배열을 변경하지 않고, 새로운 배열을 반환함
  const newArr = [...arr, 6];
  return newArr;
};

const numbers = [1, 2, 3];
const result = addSixPure(numbers);

console.log(result); // [1, 2, 3, 6]
console.log(numbers); // [1, 2, 3] (원본 변경 없음)
```

- `arr`의 원본 데이터를 변경하지 않음
- 항상 같은 입력값(`numbers`)을 넣으면 같은 출력(`result`)이 나옴


### 비순수 함수 (Impure Function)

```jsx
const num_arr = [1, 2, 3, 4, 5];

const addSixImpure = (arr) => {
  arr.push(6); // 원본 배열을 직접 변경
  return arr;
};

console.log(addSixImpure(num_arr)); // [1, 2, 3, 4, 5, 6]
console.log(num_arr); // [1, 2, 3, 4, 5, 6] (원본 변경됨)
```

문제점:

`push()`를 사용해 기존 배열 `num_arr`을 직접 변경

한 번 실행 후 다시 실행하면 결과가 다를 수도 있음 (예측 가능성이 낮아짐)



### 왜 순수 함수를 중요하게 여길까?

#### React 컴포넌트의 동작 원리와 관련

- 컴포넌트는 같은 `props`와 `state`가 주어지면 항상 같은 UI를 렌더링해야 함
- 순수 함수로 컴포넌트를 구성하면 예측 가능한 동작이 보장됨
- 불순 함수(Impure Function)가 있으면 예상치 못한 UI 변경이 발생할 수 있음



#### 상태(state)를 변경할 때 불변성을 유지

- React에서 `state`를 직접 변경하면 안 됨
- 불변 객체(Immutable Object) 방식을 유지해야 리렌더링이 올바르게 동작함

잘못된 예시 (state 직접 변경 - 비순수 함수)

```jsx
const updateUser = (user) => {
  user.name = "Brian Kang"; // 원본 객체 변경 ❌
  return user;
};
```

올바른 예시 (새로운 객체 반환 - 순수 함수)

```jsx
const updateUser = (user) => {
  return {...user, name: "Brian Kang"}; // 새로운 객체 반환 ✅
};
```

React에서는 항상 새로운 객체를 만들어 `setState` 해야 함  
불변성을 유지하면 컴포넌트가 변화 감지를 쉽게 하고 최적화됨



#### 테스트가 쉬워짐

- 같은 입력값이면 항상 같은 결과가 나오므로 테스트 코드 작성이 쉬움
- 예측 가능한 동작을 보장하여 버그 발생 확률이 줄어듦
