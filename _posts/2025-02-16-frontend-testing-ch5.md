---
title: "프런트엔드 개발을 위한 테스트 입문: 챕터 5 (항해99 사전스터디)"
date: 2025-02-16 21:00:00 +1100
categories: [ Programming, Frontend Testing ]
tags: [ Testing, 항해99 ]
author: <author_id>   
---

진짜 시간 없는게 느껴지는 빡센 날들이네요..


이해가 안가던 문장의 뜻을 알아보았는데요
> 참조를 취득하다: 특정 요소를 찾아서 조작하거나 확인할 수 있도록, 그 요소의 정보를 변수에 할당하는 과정

이라고 합니다.

--- 

## UI 컴포넌트 테스트 기초 

### MPA / SPA

과거엔 페이지 요청 단위에 기반하여 사용자와 대화하는 방식으로 개발하는 것이 일반적
여러 HTML페이지와 HTTP요청으로 만들어진 웹 어플은 MPA로 불리며, 현대적인 SPA와 비교된다 

SPA는 한개의 HTML 페이지에서 개발하는 웹 어플이다. 
웹 서버가 응답으로 보낸 최초의 HTML 페이지를 사용자 입력에 따라 부분적으로 HTML을 변경합니다
그리고 여기서 부분적으로 변경할 때 주요 대상이 되는 단위가 UI 컴포넌트다.

사용자 입력에 따라 필요한 최소한의 데이터만 취득해 화면을 갱신한다.

그렇기 때문에 응답이 빠르고, 취득에 사용되는 리소스 부담도 줄일 수 있어, 백엔드에도 간접적인 영향을 미친다.

![Screenshot 2025-02-16 at 10.16.25 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-16%20at%2010.16.25%E2%80%AFPM.png)

### UI 컴포넌트 테스트

작성 이유. 작은 컴포넌트들이 모여, 중간 크기의 UI, 들이 모여 어플리케이션이 되는데, 중간 크기가 문제가 생긴다면, 전체에 문제가 생겨 사용불가가 될수 도 있다.
그렇기에 작성해야한다. 

대표적인 UI 컴포넌트 기능을 상기하고 가야한다

- 데이터를 렌더링하는 기능
- 사용자의 입력을 전달하는 기능
- 웹 API와 연동하는 기능
- 데이터를 동적으로 변경하는 기능

이것들을 테스트 프레임워크와 라이브러리를 사용해서 의도한대로 작동하는지, 문제가 없는지를 확인해야 한다

### 접근성 테스트

접근성은 화면에 보이는 이슈가 아니기에 의식적으로 신경을 써야만 알 수 있다.
보이는 것만 보면 품질에 문제가 없다고 생각할 수 있는데,
사용자 특성에 따라 접근조차 하지 못하는 기능이 생기고 만다.
checkbox가 제일 큰 예로, 외관만 치중하고 input요소를 제거하는 경우가 있다.
이는 마우스 쓰는이는 예쁜 박스를 클릭할 수 있겠지만, 보조기를 사용하는 사용자는 체크박스 존재조차 알아차릴 수 없게 된다

접근성 향상에는 UI 컴포넌트 테스트가 안성맞춤인데, 마우스와 보조기기를 쓰는 사용자가 동일하게 요소들을 인식할 수 있는 쿼리로
테스트를 작성해야 하기때문이다.
 
## UI 컴포넌트 테스트 시작

기본적으로 테스트할 컴포넌트를 렌더링하고, 임의의 DOM을 취득한 후 DOM에 입력을 발생시키는 방식으로 진행된다.

예시로 진행하는데

계정 정보 등록 페이지에서 사용하는 컴포넌트로, 수정 버튼을 만들고, 클릭시 계정 정보 수정 화면으로 이동하는 상황을 가정해보면

![Screenshot 2025-02-16 at 11.38.27 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-16%20at%2011.38.27%E2%80%AFPM.png)

### UI 컴포넌트 렌더링

테스팅 라이브러리의 render함수를 사용해 테스트할 UI 컴포넌트를 코드와 같이 렌더링하면, 매개변수 name에 할당한 값이 그대로 화면에 표시된다.

name이 제대로 표시됐는가를 테스트 해보자
```typescript
import {render, screen} from "@testing-library/react";
import {Form} from "./Form";

test("이름을 표시한다", () => {
  render(<Form name="taro"/>);
});
```

### 특정 DOM 요소 취득하기

```screen.getByText```를 사용한다, 이 함수는 일치하는 문자열을 가진 한 개의 텍스트 요소를 찾는 API로, 요소를 발견하면 해당 요소의 참조를 취득할 수 있다.
따라서 ```screen.getByText("taro")```를 실행시 테스트할 컴포넌트를 취득하는데, 요소를 찾지못하면 오류가 발생, 테스트는 실패한다. 

### 단언문 작성

```@testing-library/jest-dom```은 '해당 요소가 DOM에 존재하는가'를 검증하는 커스텀 매처이다.
> Props에 넘겨준 이름이 표시됏는가를 테스트할 수 있다.

### 특정 DOM 요소를 역할로 취득하기

특정 DOM요소를 역할 (role)로 취득할 수 있는 ```screen.getByRole```함수가 있다
```typescript
test("버튼을 표시한다", () => {
  render(<Form name="taro" />);
  expect(screen.getByRole("button")).toBeInDocument();
});
```

이건 Form 컴포넌트안에 button이 포함 됐기때문에 성공한다
button 요소는 명시적으로 button이라는 역할을 하지 않는다
하지만, button으로 취득할 수 있는것은 암묵적 역할 이라는 식별자를 테스팅 라이브러리가 지원하기 때문이다

### heading이 포함된 단언문

```<h2>``` 같은 것도, 참조를 취득할 수 있다.
```getByRole("heading")```을 실행하면.


문자 포함 여부는 ```toHaveTextContent```라는 매처로 검증할 수 있다

```typescript
test("heading을 표시한다", () => {
  render(<Form name = "taro" />);
  expect(screen.getByRole("heading")).toHaveTextContent("계정 정보");
});
```

테스팅 라이브러리는 암묵적 역할을 활용한 쿼리를 우선적으로 사용하도록 권장한다

### 이벤트 핸들러 호출 테스트

event handler는 어떤 입력이 발생했을때 호출되는 함수를 말한다.

UI component를 작성할때, Props에 event handler를 지정해, 버튼을 클릭하면 00한다 같은 작업을 구현한다.

목 함수로 검증하고, 예로는, form의 onSubmit이벤트에 호출한 event handler를 props로 전달받는다고 했는데
event handler를 mockFn 목 함수로 전달한다.


테스트 환경에서는 버튼 클릭을 할 수 없기에, ```fireEvent.click```을 사용해 재현할 수 있다.
이러면 임의의 DOM이벤트 발생이 가능해진다.
 
```typescript
import { fireEvent, render, screen } from "@testing-library/react";

test("버튼을 클릭하면 이벤트 핸들러가 실행된다", () => {
  const mockFn = jest.fn();
  render(<Form name= "taro" onSubmit={mockFn}/> );
  fireEvent.click(screen.getByRole("button"));
  expect(mockFn).toHaveBeenCalled();
});
```
