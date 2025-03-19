---
title: "프런트엔드 개발을 위한 테스트 입문: 챕터 7 (항해99 사전스터디)"
date: 2025-03-17 15:00:00 +1100
categories: [ Programming, Frontend Testing ]
tags: [ Testing, 항해99 ]
author: <author_id>   
---

너무 나도 바쁘고, 잠도 못자는 요즘이네요.. 
그래도 3주정도 못한 프론트 테스트 코드를 하는데요
아쉽게도 챕터 6은 커버리지 리포트에 대해서 인데, 나중에 필요하면 보는걸로 하고, 7로 넘어가겠습니다

## React context와 통합 테스트 

테스트할 UI 컴포넌트 `Toast`

전역 UI, 모든 화면에서 호출할 수 있다 

이걸 다룰 때는, 한 곳에서 관리 중인 값이나 갱신 함수에 접근해야 해 `Props`만으로는 구현하는 것이 어렵다

이때, Context API를 활용하면 된다, `Props`에 명시적으로 값을 전달할 필요가 없어져, 하위 컴포넌트가 소유한 값과 갱신 함수에 직접 접근할 수 있다.

```typescript
export const initialState: ToastState = {
  isShown: false,
  message:"",
  style:"succeed",
};
```

이를 기반으로 createContext API를 사용해 Context 객체를 생성한다. 

```typescript
import { createContext } from "react";
..생략..
export const ToastStateContext = createContext(initialState); // 상태를 소유할 Context
..생략..
export const ToastActionContext = createContext(initialAction); // 상태 갱신 함수를 소유할 Context
```

```typescript
export const ToastProvider = ({
  children,
  defaultState,
}: {
  children: ReactNode;
  defaultState?: Partial<ToastState>;
}) => {
  const { isShown, message, style, showToast, hideToast } =
    useToastProvider(defaultState);
  return (
    <ToastStateContext.Provider value={{ isShown, message, style }}> {/* 하위 컴포넌트에서 isShown, message, style을 참조할 수 있다 */}
      <ToastActionContext.Provider value={{ showToast, hideToast }}> {/* 하위 컴포넌트에서 showToast, hideToast를 참조할 수 있다 */}
        {children}
        {isShown && <Toast message={message} style={style}/>} {/* isShown이 true가 되면 표시된다 */}
      </ToastActionContext.Provider>
    </ToastStateContext.Provider>
  );
};
```

Context 객체가 소유한 Provider 컴포넌트를 각각 렌더링한다. 이러면 하위 component는 최상위 것의 상태와 상태 갱신 함수를 사용할 수 있다.
isShown이 true면 화면에 `Toast`가 나타나고, 일정 시간이 지나면 false로 변경된다.

웹 API를 `onSubmit` 함수 내에 호출했을 때 호출이 성공하면 저장됐습니다, 실패시 오류가 발생했습니다 표시, 그리고 `showToast` 함수로 최상위 컴포넌트가 소유한 상태인 message와 style을 지정한다

```typescript
const { showToast } = useToastAction();
const onSubmit = handleSubmit(async() => {
  try{
     showToast({ message: "저장됐습니다", style: "succeed" });
  } catch (err) {
    showToast({ message: "오류가 발생했습니다", style: "failed"});
  }
});
```

## Context를 테스트 하는 방법

### 테스트용 컴포넌트를 만들어 인터렉션 실행

`useToastAction` 이라는 custom hook을 사용하면, 최하위 컴포넌트에서도 `toast`를 렌더링할 수 있다. 

`showToast`를 실행할 수만 있으면 되기 때문에 버튼을 클릭하면 `showToast`가 실행되도록 구현한다. 

```typescript
const TestComponent=({ message }: { message: string }) => {
  const { showToast } = useToastAction(); // <Toast>를 표시하기 위한 훅
  return <button onClick={() => showToast({ message })}>show</button>;
}
```

테스트에서 사용하는 `render` 함수로 최상위 컴포넌트인 `<ToastProvider>`와 하위 컴포넌트인 `<TestComponent>`를 렌더링한다

`await` `user.click`으로 버튼을 클릭하면 렌더링되지 않았던 `alert` 역할인 `<Toast>` 컴포넌트가 메세지와 함께 렌더링된 것을 확인할 수 있다.

```typescript

test("showToast를 호출하면 Toast컴포넌트가 표시된다", async () => {
  const message = "test";
  render(
    <ToastProvider>
      <TestComponent message = {message}
  />
  < /ToastProvider>
)
  ;
  // 처음에는 렌더링되지 않는다.
  expect(screen.queryByRole("alert")).not.toBeInTheDocument();
  await user.click(screen.getByRole("button"));
  // 렌더링됐는지 확인한다.
  expect(screen.getByRole("alert")).toHaveTextContent(message);
});

```

### 초깃값을 주입해서 렌더링된 내용 확인하기

`<ToastProvider>`는 Props에 defaultState라는 초깃값을 설정할 수 있다

렌더링 여부만 사용하고 싶다면 defaultState에 초깃값을 주입해 검증하면 된다.

```typescript
test("Succeed", () => {
  const state: ToastState = {
    isShown: true,
    message: "성공했습니다",
    style: "succeed",
  };
  render(<ToastProvider defaultState = {state} > {null} < /ToastProvider>);
  expect(screen.getByRole("alert")).toHaveTextContent(state.message);
});

test("Failed", () => {
  const state: ToastState = {
    isShown: true,
    message: "실패했습니다",
    style: "failed",
  };
  render(<ToastProvider defaultState = {state} > {null} < /ToastProvider>);
  expect(screen.getByRole("alert")).toHaveTextContent(state.message);
});
```


> `<Toast>`: `View`를 제공한다

> `<ToastProvider>` 컴포넌트: 렌더링 여부를 결정할 상태 소유

> `useToastProvider` 훅: 렌더링 관련 로직을 관리한다.

> `useToastAction` 훅 : 하위 컴포넌트에서 호출한다.

이 4모듈을 조합해 성공했습니다라고 Toast를 띄우는 기능을 제공한다 

통합 테스트의 중점도 네가지 모듈의 조합에 있다

또한, 첫 번째 방법의 테스트는 `useToastAction` (custom hook)도 포함해, 두번째 보다 더 넓은 범위의 통합 테스트이다.

## Next.js 라우터와 렌더링 통합 테스트 - deprecated XXX

header navigation을 담당하는 ui component를 테스트하는데

현재 위치를 나타내는 스타일을 활성화하는 header ui 이다

### next-router-mock

라우터 부분을 테스트하려면 목 객체를 사용해야한다. 
next-router-mock은 jest에서 nextjs의 라우터를 테스트할 수 있도록 목객체를 제공하는 라이브러리다

<Link> 컴포넌트에서 발생한 라우터 변화, useRouter를 활용한 URL 참조 혹은 변경에 대한 통합 테스트를 jsdom에서 실행할 수 있다.


### test.each

동일한 테스트를 매개변수만 변경해 반복하고 싶다면, test.each를 사용

```typescript
test.each([
  {url: "asdfasdf", name: "url1"},
  {url: "asfdasdaaa", name: "url2"}
])("$url의 현재 위치는 $name이다", ({url, name}) =>{
  mockRouter.setCurrentUrl(url);
  render(<Nav onCloseMenu={() => {}}/>);
  const link = screen.getByRole("link", {name});
  expect(link).toHaveAttribute("aria-current", "page");
});
```

