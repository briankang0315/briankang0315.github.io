---
title: "프런트엔드 개발을 위한 테스트 입문: 챕터 4 (항해99 사전스터디)"
date: 2025-02-12 21:00:00 +1100
categories: [ Programming, Frontend Testing ]
tags: [ Testing, 항해99 ]
author: <author_id>   
---

바쁘다 바뻐

--- 

### Mock 객체

테스트는 실제 실행 환경과 유사할수록 재현성이 높다, 하지만 그건 시간이 너무 걸리거나 난이도가 높을때가 많다
web API에서 가져온 데이터를 다루거나, 취득시 오류들이 생긴다. 실패하는 경우 또한 테스트 해야한다

실제 web API server를 테스트 환경과 연동 하면 성공 경우를 테스트 할 수 있지만
실패는 웹 API server에 테스트하는 코드를 추가하는 것은 옳지 않다.
그리고 API는 테스트용 구현을 추가하는것이 불가능 하다 ㅋㅋ

목 객체를 써서 API 서버를 mock하면 된다. 이러면 어려운 부분도 테스트 할 수 있고, 효율적이게 할 수 있어진다

### Stub

주로 대역으로 사용한다

- 의존중인 컴포넌트의 대역
- 정해진 값을 반환하는 용도
- 테스트 대상에 할당하는 입력값

웹 API에 의존하는 대상, "웹 API에서 이런 값을 반환받았을 때는 이렇게 작동해야 한다"와 같은 테스트에 스텁을 사용

테스트 대상이 스텁에 접근시, 정해진 값을 반환한다

![Screenshot 2025-02-12 at 9.06.36 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-12%20at%209.06.36%E2%80%AFPM.png)

### Spy

주로 기록하는 용도다

- 함수나 메서드의 호출 기록
- 호출된 횟수나 실행 시 사용한 인수 기록
- 테스트 대상의 출력 확인

테스트 대상 외부의 출력을 검증할때 사용, 
인수로 받은 callback function을 검증 (대표적)

콜백 함수의 실행 횟수, 실행 시 사용한 인수 등 기록하기에 의도대록 콜백이 호출됐는지 검증 가능

![Screenshot 2025-02-12 at 9.09.18 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-12%20at%209.09.18%E2%80%AFPM.png)


### Stub 만들기

단위 테스트나 통합 테스트를 실시할 때 구현이 완성되어 있지 않거나 수정이 필요한 모듈에 의존 중인 경우가 있다
이때 stub으로 대체시 테스트 할 수 없던 대상을 테스트 할 수 있게 된다 

![Screenshot 2025-02-12 at 9.10.57 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-12%20at%209.10.57%E2%80%AFPM.png)

#### 테스트할 함수 (예시)

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

#### 모듈을 스텁으로 대체

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

#### 일부 모듈을 스텁으로 대체하기
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

#### 라이브러리 대체하기
수정이 필요한 모듈의 일부를 대체하는 방법
목 모듈을 실무에서 제일 많이 사용한다

### 웹 API MOCK 객체 기초
웹 앱에서 웹 API 서버와 통신하여 데이터를 취득, 갱신하는 작업은 필수

테스트시 웹 API 관련 코드를 웹 API 클라의 대역인 STUB으로 대체, 테스트를 작성한다
실제 응답은 아니지만, 응답 전후의 관련 코드를 검증할때 유용하게 사용할 수 있다 

![Screenshot 2025-02-12 at 9.29.36 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-12%20at%209.29.36%E2%80%AFPM.png)

#### 테스트할 함수

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

#### Web API Client Stub 구현
jest.spyOn 을 사용해서. (TS와 상성 좋다)

```typescript
import * as Fetchers from "./fetchers";
jest.mock("./fetchers");
jest.spyOn(Fetchers, "getMyProfile") // (테스트할 객체, 함수 이름) 
```

##### 데이터 취득 성공 재현 

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
##### 데이터 취득 실패 재현 

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
