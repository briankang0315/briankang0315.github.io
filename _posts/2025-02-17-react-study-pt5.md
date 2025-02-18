---
title: "리액트 스터디 [Hooks] (항해99 사전스터디)"
date: 2025-02-17 13:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---

## 렌더링 트리거

첫 리액트 앱을 시작했을때

state 변경 발생했을때
- 컴포넌트 내부 state change
- 컴포넌트에 새로운 props 들어올 때
- 상위 부모 컴포넌트에서 위에 두 이유로 렌더링이 발생했을때 

--- 
 
State를 통해 컴포넌트는 사용자 입력과 같은 정보를 기억할 수 있습니다.

폼 컴포넌트는 state를 사용해 입력값을 저장할 수 있고, 이미지 컴포넌트는 선택한 이미지 인덱스를 저장할 수 있습니다.

## 컴포넌트에 State 추가

### useState

직접 업데이트할 수 있는 State 변수를 선언합니다
- 컴포넌트 내부에서 직접 상태를 관리할 때 사용
- 상태가 변경되면 컴포넌트가 다시 렌더링됨
```typescript
const [state, setState] = useState(initialState)
```

### useReducer 

Reducer function 내부의 업데이트 로직을 사용하여 State 변수를 선언합니다
- 상태 업데이트 로직을 함수로 관리 → dispatch(action)을 통해 상태 변경
- 여러 상태를 한 곳에서 관리해야 할 때 유용

```typescript
const [state, dispatch] = useReducer(reducer, initialArg, init?)
```

## 컴포넌트로부터 정보를 전달 받음

### useContext 

Context는 컴포넌트가 Props를 전달하지 않고도 멀리 있는 부모 컴포넌트로부터 정보를 받을 수 있게 해줍니다.
- Context를 통해 멀리 떨어진 컴포넌트에도 데이터 전달 가능
- Props drilling 없이 공유할 정보(예: 테마, 사용자 정보) 관리
```typescript
const value = useContext(SomeContext)
```

## 정보 보유

컴포넌트가 렌더링에 사용되지 않는 일부 정보를 보유할 수 있습니다. 
Ref는 업데이트를 해도 컴포넌트가 <font color="orange">다시 렌더링 되지 않습니다</font>
Ref는 React패러다임의 탈출구입니다. 
내장된 browser api 같이, react가 아닌 시스템으로 작업해야 할 때 유용합니다.

### useRef
- 값이 변경되어도 렌더링이 발생하지 않음
- <font color="olivegrab">DOM 요소 접근</font>, 이전 값 저장, 타이머 핸들링 등에 유용
```typescript
const ref = useRef(initialValue)
```

## 외부 시스템 연결, 동기화

### useEffect
- API 호출, 구독(subscribe), DOM 변경 처리
- 의존성 배열을 통해 특정 값이 변경될 때만 실행
- <font color="olivegrab">사이드 이팩트 관리</font>
```typescript
useEffect(setup, dependencies?)
```
useEffect는 외부 시스템과 컴포넌트를 동기화하는 React Hook입니다.

## 캐싱

### useMemo

리렌더링 사이에 계산 결과 캐싱
- 비싼 연산(예: 필터링, 정렬, 데이터 가공)을 캐싱하여 불필요한 계산 방지
- <font color="olivegrab">성능 최적화</font>
```typescript
const cachedValue = useMemo(calculateValue, dependencies)
```

### useCallback
리렌더링 간에 함수 정의를 캐싱
- 함수가 리렌더링 때마다 새로 정의되는 것을 방지
- Props로 전달하는 함수 최적화 (예: onClick 핸들러)
- <font color="olivegrab">성능 최적화</font>
```typescript
const cachedFn = useCallback(fn, dependencies)
```
