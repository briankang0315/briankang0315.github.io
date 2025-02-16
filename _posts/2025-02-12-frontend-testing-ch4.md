---
title: "프런트엔드 개발을 위한 테스트 입문: 챕터 4 (항해99 사전스터디)"
date: 2025-02-12 21:00:00 +1100
categories: [ Programming, Frontend Testing ]
tags: [ Testing, 항해99 ]
author: <author_id>   
---

바쁘다 바뻐

--- 

## Mock 객체

테스트는 실제 실행 환경과 유사할수록 재현성이 높다, 하지만 그건 시간이 너무 걸리거나 난이도가 높을때가 많다
web API에서 가져온 데이터를 다루거나, 취득시 오류들이 생긴다. 실패하는 경우 또한 테스트 해야한다

실제 web API server를 테스트 환경과 연동 하면 성공 경우를 테스트 할 수 있지만
실패는 웹 API server에 테스트하는 코드를 추가하는 것은 옳지 않다.
그리고 API는 테스트용 구현을 추가하는것이 불가능 하다 ㅋㅋ

목 객체를 써서 API 서버를 mock하면 된다. 이러면 어려운 부분도 테스트 할 수 있고, 효율적이게 할 수 있어진다

## Stub

주로 대역으로 사용한다

- 의존중인 컴포넌트의 대역
- 정해진 값을 반환하는 용도
- 테스트 대상에 할당하는 입력값

웹 API에 의존하는 대상, "웹 API에서 이런 값을 반환받았을 때는 이렇게 작동해야 한다"와 같은 테스트에 스텁을 사용

테스트 대상이 스텁에 접근시, 정해진 값을 반환한다

![Screenshot 2025-02-12 at 9.06.36 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-12%20at%209.06.36%E2%80%AFPM.png)

## Spy

주로 기록하는 용도다

- 함수나 메서드의 호출 기록
- 호출된 횟수나 실행 시 사용한 인수 기록
- 테스트 대상의 출력 확인

테스트 대상 외부의 출력을 검증할때 사용, 
인수로 받은 callback function을 검증 (대표적)

콜백 함수의 실행 횟수, 실행 시 사용한 인수 등 기록하기에 의도대록 콜백이 호출됐는지 검증 가능

![Screenshot 2025-02-12 at 9.09.18 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-12%20at%209.09.18%E2%80%AFPM.png)


## Stub 만들기

단위 테스트나 통합 테스트를 실시할 때 구현이 완성되어 있지 않거나 수정이 필요한 모듈에 의존 중인 경우가 있다
이때 stub으로 대체시 테스트 할 수 없던 대상을 테스트 할 수 있게 된다 

![Screenshot 2025-02-12 at 9.10.57 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-12%20at%209.10.57%E2%80%AFPM.png)

### 테스트할 함수 (예시)

```typescript
export function greet(name: string){
  return `Hello! ${name}`;
}

export function sayGoodBye(name:string){
  return new Error("미구현");
}
```

```typescript
import {greet} from "./greet"

jest.mock("./greet");

test("인사말을 반환하지 않는다 (원래 구현과 다르게)", () => {
  expect(greet("Brian")).toBe(undefined);
});
```

jest.mock 이 테스트 전에 호출되며, 테스트할 모듈을 대체했다, 그래서 undefined가 반환된다 (greet는 구현 되었는데도)

### 모듈을 스텁으로 대체

```typescript
import {greet, sayGoodBye} from "./greet"

jest.mock("./greet", () => ({
  sayGoodBye: (name: string) => `Good bye, ${name}`,
}));

test("인사말을 반환하지 않는다 (원래 구현과 다르게)", () => {
  expect(greet).toBe(undefined);
});

test("작별 인사를 반환한다 (원래 구현과 다르게)", () => {
  const message = `${sayGoodBye("Brian")} See you`;
  expect(message).toBe("Good bye, Brian See you.")
});
```

### 일부 모듈을 스텁으로 대체하기
```jest.requireActual``` 함수를 사용시, 원래 모듈의 구현을 import할 수 있다. 이 함수로 sayGoodBye만 대체할 수 있게 된다.

```typescript
import {greet, sayGoodBye} from "./greet"

jest.mock("./greet", () => ({
  ...jest.requireActual("./greet"),
  sayGoodBye: (name: string) => `Good bye, ${name}`,
}));

test("인사말을 반환한다 (원래 구현대로)", () => {
  expect(greet("Brian")).toBe("Hello! Brian");
});

test("작별 인사를 반환한다 (원래 구현과 다르게)", () => {
  const message = `${sayGoodBye("Brian")} See you`;
  expect(message).toBe("Good bye, Brian See you.")
});
```

### 라이브러리 대체하기
수정이 필요한 모듈의 일부를 대체하는 방법
목 모듈을 실무에서 제일 많이 사용한다

## 웹 API MOCK 객체 기초
웹 앱에서 웹 API 서버와 통신하여 데이터를 취득, 갱신하는 작업은 필수

테스트시 웹 API 관련 코드를 웹 API 클라의 대역인 STUB으로 대체, 테스트를 작성한다
실제 응답은 아니지만, 응답 전후의 관련 코드를 검증할때 유용하게 사용할 수 있다 

![Screenshot 2025-02-12 at 9.29.36 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-12%20at%209.29.36%E2%80%AFPM.png)

### 테스트할 함수

fetchAPI 나 Axios를 써서 웹 API 클라를 구현하는데
그렇게 취득한 데이터는 가공된 뒤 렌더링 된다.

fetchers는 이제 api 호출 로직. 생략.

```typescript
import {getMyProfile} from "../fetchers";

export async function getGreet() {
  const data = await getMyProfile();
  if (!data.name){
    return `hello anonymous user`;
  }
  return `hello ${data.name}`;
}
```
근데 이제 요청에 응답할 API server가 없으면? getGreet는 테스트 불가하다

그렇기에 스텁으로 getMyProfile을 대체해 응답 여부 상관 없이 데이터 취득과 관련된 로직을 테스트 할 수 있다.

### Web API Client Stub 구현
jest.spyOn 을 사용해서. (TS와 상성 좋다)

```typescript
import * as Fetchers from "./fetchers";
jest.mock("./fetchers");
jest.spyOn(Fetchers, "getMyProfile") // (테스트할 객체, 함수 이름) 
```

#### 데이터 취득 성공 재현 

```typescript
jest.spyOn(Fetchers, "getMyProfile").mockResolvedValueOnce({
  id: "123123412412",
  email: 'testtest@test.com'
})
```

취득 성공 (resolve)응답으로 기대하는 객체를 ```mockResolvedValueOnce```에 지정한다
유지보수 뛰어남.

```typescript
test("데이터 취득 성공 시: 사용자 이름이 없는 경우", async() => {
  // getMyProfile이 resolve되었을때 값을 재현
  jest.spyOn(Fetchers, "getMyProfile").mockResolvedValueOnce({
    id:"12312412451"
    email: 'testtest@test.com',
  });
  await expect(getGreet()).resolves.toBe("Hello, anonymous user");
});

test("데이터 취득 성공 시: 사용자 이름이 있는 경우", async () => {
  // getMyProfile이 resolve되었을때 값을 재현
  jest.spyOn(Fetchers, "getMyProfile").mockResolvedValueOnce({
    id: "12312412451"
    email: 'testtest@test.com',
    name: 'Brian'
  });
  await expect(getGreet()).resolves.toBe("Hello, Brian");
});
```
#### 데이터 취득 실패 재현 

HTTP 상태 코드가 200~299 외 값일땐 (res.ok가 falsy) 함수에서 예외를 발생시킨다

data와 함께 예외를 발생시키면, getMyProfile 함수가 반환하는 Promise는 reject된다

```typescript
export const httpError: HttpError = {
  err: {message: 'internal server error'},
};
```

이게 오류 객체인데, ```mockRejectedValueOnce``` 인수로 getMyProfile 함수의 reject를 재현하는 stub을 구현

```typescript
jest.spyOn(Fetchers, "getMyProfile").mockRejectedValueOnce(httpError);
```

이거 덕에 데이터 취득 실패시 관련 코드가 어떻게 작동하는지 테스트 할 수 있게 된다

```typescript
test("데이터 취득 실패 시", async() => {
  jest.spyOn(Fetchers, "getMyProfile").mockRejectedValueOnce(httpError);
  await expect(getGreet()).rejects.toMatchObject({
    err: { message: "internal server error"},
  });
});
```

여기에 추가로 예외가 발생하고 있는지 검증하고 싶다면, 

```typescript
test("데이터 취득 실패 시 오류가 발생한 데이터와 함께 예외 throw",
async () => {
  expect.assertions(1);
  jest.spyOn(Fetchers, "getMyProfile").mockRejectedValueOnce(httpError);
  try{
    await getGreet();
  } catch (err){
    expect(err).toMatchObject(httpError);
  }
});

```

## 웹 API MOCK 객체 생성 함수

- 웹 API 응답을 고정된 스트림 대신 목(Mock) 객체를 사용하여 대체하는 방법
- 가공된 응답 데이터를 활용하여 테스트를 진행합니다

### 테스트할 함수

getMyArticleLinksByCategory

- 로그인한 사용자가 작성한 기사 링크 목록을 가져오는 함수.
- 특정 태그를 포함한 기사만 필터링하여 반환.
- 데이터 취득 실패 시 예외 처리.

```typescript
export async function getMyArticleLinksByCategory(category: string) {
  const data = await getMyArticles();
  const articles = data.articles.filter((article) =>
    article.tags.includes(category)
  );

  if (!articles.length) {
    return null;
  }

  return articles.map((article) => ({
    title: article.title,
    link: `/articles/${article.id}`,
  }));
}
```

- `getMyArticles()` 함수로 데이터를 불러온 후, 특정 `category`를 포함하는 기사만 필터링.
- 해당하는 기사가 없으면 `null` 반환.
- 있을 경우 `{ title, link }` 형식으로 변환하여 반환.

### 응답을 교체하는 목 객체 생성 함수

생성 과정

- `getMyArticles` 함수의 응답을 대체하기 위한 픽스처(fixture) 데이터 정의.
- 실제 API를 호출하지 않고 고정된 데이터를 반환하도록 함.

```typescript
export const getMyArticlesData: Articles = {
  articles: [
    {
      id: "howto-testing-with-typescript",
      createdAt: "2022-07-19T22:38:41.005Z",
      tags: ["testing"],
      title: "타입스크립트를 사용한 테스트 작성법",
      body: "테스트 작성 시 타입스크립트를 사용하면 유지보수가 쉬워진다",
    },
    {
      id: "nextjs-link-component",
      createdAt: "2022-07-19T22:38:41.005Z",
      tags: ["nextjs"],
      title: "Next.js의 링크 컴포넌트",
      body: "Next.js는 화면을 이동할 때 링크 컴포넌트를 사용한다",
    },
    {
      id: "react-component-testing-with-jest",
      createdAt: "2022-07-19T22:38:41.005Z",
      tags: ["testing", "react"],
      title: "제스트로 시작하는 리액트 컴포넌트 테스트",
      body: "제스트는 단위 테스트처럼 UI 컴포넌트를 테스트할 수 있다",
    },
  ],
};
```

- `articles` 배열에 가상의 기사 데이터를 포함.
- `tags` 배열을 이용해 특정 태그를 필터링할 수 있도록 구성.

```typescript
function mockGetMyArticles(status = 200) {
  if (status > 299) {
    return jest.spyOn(/ Fetchers /, "getMyArticles")
      .mockRejectedValueOnce({ err: { message: "internal server error" } });
  }
  return jest.spyOn(/ Fetchers /, "getMyArticles")
    .mockResolvedValueOnce(getMyArticlesData);
}
```

- API 호출을 대체하여 목 응답을 반환하는 유틸리티 함수.
- 상태 코드가 300 이상이면 오류 객체 반환.
- 정상적인 응답이면 `getMyArticlesData` 반환.


### 데이터 취득 성공을 재현한 테스트

#### 목 객체를 사용하여 성공적인 데이터 취득 테스트

```typescript
test("지정한 태그를 포함한 기사가 한 건도 없으면 null을 반환한다", async () => {
  mockGetMyArticles();
  const data = await getMyArticleLinksByCategory("playwright");
  expect(data).toBeNull();
});
```

- `playwright` 태그를 포함한 기사가 없으므로 `null` 반환이 예상됨.

```typescript
test("지정한 태그를 포함한 기사가 한 건 이상 있으면 링크 목록을 반환한다", async () => {
  mockGetMyArticles();
  const data = await getMyArticleLinksByCategory("testing");
  expect(data).toMatchObject([
    {
      link: "/articles/howto-testing-with-typescript",
      title: "타입스크립트를 사용한 테스트 작성법",
    },
    {
      link: "/articles/react-component-testing-with-jest",
      title: "제스트로 시작하는 리액트 컴포넌트 테스트",
    },
  ]);
});
```

- `testing` 태그를 포함한 기사가 있으므로 링크 목록이 올바르게 반환되는지 확인.


#### 데이터 취득 실패를 재현한 테스트

```typescript
test("데이터 취득에 실패하면 reject된다", async () => {
  mockGetMyArticles(500);
  await getMyArticleLinksByCategory("testing").catch((err) => {
    expect(err).toMatchObject({
      err: { message: "internal server error" },
    });
  });
});
```

- `mockGetMyArticles(500)`을 호출하여 실패 응답을 시뮬레이션.
- `.catch` 블록에서 예외 객체가 올바르게 반환되는지 검증.


- 테스트 환경에서 API 요청을 실제로 보내지 않고도 목 객체를 활용하여 검증 가능.
- 픽스처 데이터를 활용하여 다양한 테스트 케이스를 적용할 수 있음.
- 유틸리티 함수를 활용하면 반복적인 `jest.spyOn` 코드 작성을 줄이고 가독성을 높일 수 있음.
- 데이터 취득 성공/실패 케이스를 분리하여 철저한 테스트 진행 가능.

## 목 함수를 사용하는 스파이


스파이는 테스트 대상에서 발생한 입력값을 기록하는 객체입니다. 기록된 값을 검증하여 의도한 대로 기능이 작동하는지 확인할 수 있습니다.

### 실행됐는지 검증하기

jest.fn()을 사용해 목 함수를 작성합니다. toBeCalled 매처를 사용하면 실행 여부를 검증할 수 있습니다.

```typescript
test("목 함수가 실행됐다", () => {
  const mockFn = jest.fn();
  mockFn();
  expect(mockFn).toBeCalled();
});
```

### 실행 횟수 검증

toHaveBeenCalledTimes 매처를 사용하면 목 함수가 몇 번 호출됐는지 검증할 수 있습니다.

```typescript
test("목 함수는 실행 횟수를 기록한다", () => {
  const mockFn = jest.fn();
  mockFn();
  expect(mockFn).toHaveBeenCalledTimes(1);
  mockFn();
  expect(mockFn).toHaveBeenCalledTimes(2);
});
```

### 실행 시 인수 검증

목 함수 실행 시 인수도 기록할 수 있습니다. 실행했을 때 인수를 검증하기 위해 함수 내부에서 호출된 목 함수를 확인합니다.

```typescript
test("목 함수는 실행 시 인수를 기록한다", () => {
  const mockFn = jest.fn();
  function greet(message: string) {
    mockFn(message);
  }
  greet("hello");
  expect(mockFn).toHaveBeenCalledWith("hello");
});
```

### 스파이로 활용하는 방법

테스트 대상의 인수에 함수가 있을 때 유용하게 활용할 수 있습니다. greet 함수의 두 번째 인수를 스파이로 활용할 수 있습니다.

```typescript
export function greet(name: string, callback?: (message: string) => void) {
  callback?.(`Hello! ${name}`);
}
```

```typescript
test("목 함수를 테스트 대상의 인수로 사용할 수 있다", () => {
  const mockFn = jest.fn();
  greet("Jiro", mockFn);
  expect(mockFn).toHaveBeenCalledWith("Hello! Jiro");
});
```

### 실행 시 인수가 객체일 때의 검증

객체를 인수로 넘길 경우 toHaveBeenCalledWith을 사용해 검증할 수 있습니다.

```typescript
const config = {
  mock: true,
  feature: { spy: true },
};

export function checkConfig(callback?: (payload: object) => void) {
  callback?.(config);
}
```

```typescript
test("목 함수는 실행 시 인수가 객체일 때에도 검증할 수 있다", () => {
  const mockFn = jest.fn();
  checkConfig(mockFn);
  expect(mockFn).toHaveBeenCalledWith({
    mock: true,
    feature: { spy: true },
  });
});
```

expect.objectContaining을 사용하면 객체의 일부만 검증할 수도 있습니다.

```typescript
test("expect.objectContaining을 사용한 부분 검증", () => {
  const mockFn = jest.fn();
  checkConfig(mockFn);
  expect(mockFn).toHaveBeenCalledWith(
    expect.objectContaining({
      feature: { spy: true },
    })
  );
});
```

## 웹 API 목 객체의 세부 사항

입력값을 검증한 후 응답 데이터를 교체하는 목 객체의 구현 방법을 알아봅니다.

### 테스트할 함수

백엔드에서 전달받는 데이터를 저장하기 전에 유효성 검사를 실행하는 checkLength 함수를 작성합니다.

```typescript
export class ValidationError extends Error {}

export function checkLength(value: string) {
  if (value.length === 0) {
    throw new ValidationError("한 글자 이상의 문자를 입력해주세요");
  }
}
```

### 목 객체 생성 함수 만들기

mockPostMyArticle 함수를 생성하여 입력값 검증 후 응답을 반환하도록 합니다.

```typescript
function mockPostMyArticle(input: ArticleInput, status = 200) {
  if (status > 299) {
    return jest.spyOn(Fetchers, "postMyArticle").mockRejectedValueOnce(httpError);
  }
  try {
    checkLength(input.title);
    checkLength(input.body);
    return jest.spyOn(Fetchers, "postMyArticle").mockResolvedValueOnce({ ...postMyArticleData, ...input });
  } catch (err) {
    return jest.spyOn(Fetchers, "postMyArticle").mockRejectedValueOnce(httpError);
  }
}
```

### 테스트 준비

팩토리 함수를 작성하여 입력값을 동적으로 생성할 수 있도록 합니다.

```typescript
function inputFactory(input?: Partial<ArticleInput>) {
  return {
    tags: ["testing"],
    title: "타입스크립트를 사용한 테스트 작성법",
    body: "테스트 작성 시 타입스크립트를 사용하면 유지보수가 쉬워진다",
    ...input,
  };
}
```

### 유효성 검사 성공 재현 테스트

입력값을 포함한 응답을 반환하는지 검증합니다.

```typescript
test("유효성 검사에 성공하면 응답을 반환한다", async () => {
  const input = inputFactory();
  const mock = mockPostMyArticle(input);
  const data = await postMyArticle(input);
  expect(data).toMatchObject(expect.objectContaining(input));
  expect(mock).toHaveBeenCalled();
});
```

### 유효성 검사 실패 재현 테스트

유효성 검사에서 실패하면 reject되는지 검증합니다.

```typescript
test("유효성 검사에 실패하면 reject된다", async () => {
  expect.assertions(2);
  const input = inputFactory({ title: "", body: "" });
  const mock = mockPostMyArticle(input);
  await postMyArticle(input).catch((err) => {
    expect(err).toMatchObject({ err: { message: expect.anything() } });
    expect(mock).toHaveBeenCalled();
  });
});
```

### 데이터 취득 실패 재현 테스트

데이터 취득이 실패하는 상황에서도 reject되는지 검증합니다.

```typescript
test("데이터 취득에 실패하면 reject된다", async () => {
  expect.assertions(2);
  const input = inputFactory();
  const mock = mockPostMyArticle(input, 500);
  await postMyArticle(input).catch((err) => {
    expect(err).toMatchObject({ err: { message: expect.anything() } });
    expect(mock).toHaveBeenCalled();
  });
});
```

## 현재 시각에 의존하는 테스트


테스트 로직이 현재 시각에 따라 결과가 달라지는 경우, 실행 환경에 따라 테스트가 불안정해질 수 있습니다. 이러한 문제를 해결하기 위해 테스트 실행 시 현재 시간을 고정하면 언제 실행하더라도 같은 테스트 결과를 얻을
수 있습니다.

### 테스트할 함수

아침, 점심, 저녁마다 다른 인사말을 반환하는 함수입니다. 실행 시각에 따라 반환값이 달라집니다.

```typescript
export function greetByTime() {
  const hour = new Date().getHours();
  if (hour < 12) {
    return "좋은 아침입니다";
  } else if (hour < 18) {
    return "식사는 하셨나요";
  }
  return "좋은 밤 되세요";
}
```

### 현재 시각 고정하기

테스트 실행 환경의 현재 시각을 임의의 값으로 고정하려면 다음과 같은 Jest 함수를 사용합니다.

- `jest.useFakeTimers()`: 가짜 타이머를 사용하도록 설정합니다.
- `jest.setSystemTime()`: 가짜 타이머에서 사용한 현재 시간을 설정합니다.
- `jest.useRealTimers()`: 실제 타이머를 사용하도록 설정합니다.

```typescript
describe("greetByTime()", () => {
  beforeEach(() => {
    jest.useFakeTimers();
  });

  afterEach(() => {
    jest.useRealTimers();
  });

  test("아침에는 '좋은 아침입니다'를 반환한다", () => {
    jest.setSystemTime(new Date(2023, 4, 23, 8, 0, 0));
    expect(greetByTime()).toBe("좋은 아침입니다");
  });

  test("점심에는 '식사는 하셨나요'를 반환한다", () => {
    jest.setSystemTime(new Date(2023, 4, 23, 14, 0, 0));
    expect(greetByTime()).toBe("식사는 하셨나요");
  });

  test("저녁에는 '좋은 밤 되세요'를 반환한다", () => {
    jest.setSystemTime(new Date(2023, 4, 23, 21, 0, 0));
    expect(greetByTime()).toBe("좋은 밤 되세요");
  });
});
```

### 설정과 파기

테스트 실행 전에 공통적으로 설정해야 할 작업이 있거나, 테스트가 끝난 후 해제해야 할 작업이 있는 경우 beforeAll, beforeEach, afterAll, afterEach를 사용할 수 있습니다.

```typescript
beforeAll(() => console.log("1 - beforeAll"));
afterAll(() => console.log("1 - afterAll"));
beforeEach(() => console.log("1 - beforeEach"));
afterEach(() => console.log("1 - afterEach"));

test("", () => console.log("1 - test"));

describe("Scoped / Nested block", () => {
  beforeAll(() => console.log("2 - beforeAll"));
  afterAll(() => console.log("2 - afterAll"));
  beforeEach(() => console.log("2 - beforeEach"));
  afterEach(() => console.log("2 - afterEach"));

  test("", () => console.log("2 - test"));
});
```
실행 순서

```
1 - beforeAll
1 - beforeEach
1 - test
1 - afterEach
2 - beforeAll
1 - beforeEach
2 - beforeEach
2 - test
2 - afterEach
1 - afterEach
2 - afterAll
1 - afterAll
```

- `beforeAll`은 한 번 실행되며, `beforeEach`는 각 테스트마다 실행됩니다.
- `afterEach`는 각 테스트 후 실행되며, `afterAll`은 전체 테스트가 끝난 후 실행됩니다.
