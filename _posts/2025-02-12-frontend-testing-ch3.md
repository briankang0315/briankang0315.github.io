---
title: "프런트엔드 개발을 위한 테스트 입문: 챕터 3 (항해99 사전스터디)"
date: 2025-02-12 12:00:00 +1100
categories: [ Programming, Frontend Testing ]
tags: [ Testing, 항해99 ]
author: <author_id>   
---

Jest를 사용해서 테스트를 구현한다고 해, 새로운 것들을 추가로 배우게 되어서 기분이 좋네요

---
### 제스트의 test 함수
일단 테스트는 제스트가 제공하는 ```test``` 함수로 정의하기로 합니다

```test```는 2개의 매개변수(parameter)를 받을수 있습니다.

```test(테스트 명, 테스트 함수);```
이런식으로 진행을 하고
예시로는 

```typescript
test("1 + 2는 3", () =>{
  expect(검증).toBe(기댓);
});
```

### 테스트 그룹 작성
연관성있는 테스트들을 그룹화 시키고 싶다면, ```describe``` 함수를 쓴다
```describe(그룹명, 그룹 함수);``` 형식으로

```typescript
describe("add", () => {
  test("1 + 1은 2", () => {
    expect(add(1,1)).toBe(2);
  });
  test("1 + 2는 3", () => {
    expect(add(1,2).toBe(3));
  });
});
```

중첩도 가능하다, ```test```는 불가하지만

```typescript
describe("사칙연산", () => {
  describe("add", () => {
    ...
  });
  describe("sub", () => {
    ...
  });
});
```

### 테스트 실행 방법


#### 명령줄 인터페이스
```package.json```에 ```npm script```추가

```typescript
{
  "scripts": {
    "test": "jest"
  }
}
```
추가하고 나면 ```npm test```으로 전체 코드 테스트를 실행할 수 있다 

특정 파일만 실행하고 싶으면

```npm test 'src/03/02/index.test.ts``` 같이 할 수 있다

#### Jest Runner 

파일명을 틀리기 쉽기에, IDE에 extension을 찾자.

#### 실행 결과 확인
테스트 파일 마다 한 줄 씩 표시된다 (앞에 ```PASS```)

전부 성공했다면 summary가 나온다

실패한 테스트가 있다면, ```FAIL```이 붙고, 실패한 부분과 이유가 자세히 기술되어 있다.

Summary에 FAIL이 하나 떴는데, 테스트 코드에도, 대상에도 문제가 없는데 실패한다면, 버그가 있다는 의미여서 
테스트 결과를 단서로 버그를 찾아야한다

### 조건 분기

테스트는 테스트할 모듈이 의도(사양)대로 구현됐는지 검증할 때 도움이 된다.
복잡할수록 조건 분기에서 버그가 많이 생긴다. 그래서 특별히 주의하면서 테스트를 작성해야 한다

테스트의 이름, 테스트 명은, 함수 기능에, 조건에 부합하는 적절한 이름으로 변경해야한다

예로 합이 add의 상한을 100으로 제한하는 코드를 추가
테스트 코드는
```typescript
test("70 + 80는 100", () => {
  expect(add(70, 80).toBe(100));
});
```
테스트는 통과 하겟지만, 70 + 80 = 100 이 된다는건 이해하기 쉽지 않다.
그러기에

```typescript
test("반환값의 상환은 '100'이다", () => {
  expect(add(70, 80).toBe(100));
});
```

이렇게 테스트 코드가 어떤 의도로 작성됐으며, 어떤 작업이 포함되었는지 테스트 명으로 명확하게 표현해야 한다.

### Edge case와 예외 처리

모듈을 사용할때 실수등의 이유로 예상치못한 입력값을 보낼 때가 있다.
만약 예상치못한 값을 받았을땐, debugger로 빨리 발견할 수 있다. 

#### Typescript로 입력값 제약 설정

type annotation을 붙히면, 실행하기 전에 오류가 발생한다.

```typescript
export function add(a:number, b:number){
  ...
}

add(1,2); // ok
add("1", "2"); // Error
```

그렇지만 정적 타입을 붙히는것만으로 부족할 때가 있다.
특정 범위로 입력값을 제한하고 싶을때는 런타임에 예외를 발생시키는 처리를 추가해야 한다.


#### 예외 발생시키기 

a,b는 0~100까지 숫자만 받을 수 있다는 조건을 추가하면
type annotation으로만은 한계가 있다

```typescript
test("반환값의 상한은 '100'이다", () =>{
  expect(add(-10,100)).toBe(100);
});
```
그렇기에 입력값이 기댓값과 다르면 예외를 발생시켜 값을 반환하기전에 중지시키는 코드를 추가하면 된다

```typescript
export function add(a:number, b:number){
  if (a<0 || a>100){
    throw new Error("0~100 사이의 값을 입력해주세요");
  }
  if (b<0 || b>100){
    throw new Error("0~100 사이의 값을 입력해주세요");
  }
  const sum = a + b;
  if (sum > 100){
    return 100;
  }
  return sum;
}
```

#### 예외 발생 검증 테스트

범위 밖의 값을 할당하면 예외를 발생시킨다 가 주 목적이기에

```expect``` 인수(argument)로 값이 아닌 예외가 발생하는 함수를 할당
matcher로 ```toThrow```를 사용한다

```typescript
expect(예외가 발생하는 함수).toThrow();
```
arrow function을 사용해야한다
```typescript
expect(() => add(-10, 100)).toThrow();
```

에외가 발생하지 않은 입력값을 테스트 하면 테스트가 실패한다.

> 의도적으로 예외를 발생시키기도 하지만 의도치 않은 버그가 생겨서 발생할 때도 있다.
> 의도한 대로 예외가 발생하고 있는가 라는 관점으로 접근하자.

#### instanceof 연산자로 세부 사항 검증

```typescript
export class HttpError extends Error{}
export class RangeError extends Error{}

if (err instanceof HttpError){
  // 오류가 HttpError인 경우 
}
if (err instanceof RangeError){
  // 발생한 오류가 RangeError인 경우
}
```

RangeError 인스턴스를 throw 하는 함수

```typescript
function checkRange(value: number){
  if (value < 0 || value > 100){
    throw new RangeError("0~100 사이의 값을 입력해주세요");
  }
}
```

전 코드에 접목시키면
```typescript
export function add (a: number, b:number){
  checkRange(a);
  checkRange(b);
  const sum = a+b;
  ...
}
```

이렇게 checkRange 한 곳에서 처리할수 있어서 더욱 좋은 코드가 된다.

테스트 검증에도 사용될 수 있다
```toThrow```에는 메세지만이 아니라 클래스도 할당 가능하다
그래서 발생한 예외가 특정 클래스의 인스턴스인지 검증할 수 있다.

```typescript
expect(() => add(110, -10)).toThrow(HttpError); // 발생한 예외가 RangeError이므로 실패한다 
expect(() => add(110, -10)).toThrow(RangeError); // 발생한 예외가 RangeError이므로 성공한다
expect(() => add(100, -10)).toThrow(Error); // 발생한 예외가 Error를 상속받은 클래스 이므로 성공한다
```

3 번째처럼 부모클래스 Error를 인수로 지정하는것은 주의해야 한다.
상속받았기에 테스트가 성공한다, 세부적으로 오류를 구분하고자 나눴던거기에, RangeError (2번)이 적절하다.
 
### 용도별 Matcher

#### 진릿값 검증 (truth value)
```toBeTruthy```는 참 값과 일치하는 매처
```toBeFalsy```는 거짓.

각 매처 앞에 ```not```을 붙히면 반전시킬 수 있다.

```typescript
 expect(0).not.toBeTruthy();
```

null , undefined도 toBeFalsy 와 일치한다
하지만, null 인지 undefined인지 검증하고 싶을땐, 

```typescript 
expect(null).toBeNull(); 
expect(undefined).not.toBeUndefined();
```
을 사용하는 것이 좋다

#### 수치 검증 equality

```typescript
toEqual();
toBeGreaterThan();
toBeGreaterThanOrEqual(); // Less 도 같게
```
계산한 소숫값 검증할때는 ```toBeCloseTo()```
두번째 인수에는 자릿수.

#### 문자열 검증
```typescript
toContain(); // 문자열 일치 검증
toMatch(); // regular expression 검증
toHaveLength(); // 길이 검증
stringContaining(); // 객체에 포함된 문자열 검증 (property중 기댓값의 일부가 포함시 성공)
stringMatching(); // 객체에 포함된 문자열 검증 (property중 기댓값의 일부가 포함시 성공)
```

#### 배열 검증
```typescript
toContain(); // primitive type의 특정값이 포함됐는지 확인
toHaveLength(); // 배열 길이 검증
toContainEqual(); // 특정 객체 포함 확인 (등가 비교)
arrayContaining(); // 인수로 넘겨준 배열 요소들 전부 포함시 성공 (등가 비교)
```

#### 객체 검증 
```typescript
toMatchObject({name: "Brian", age: 23}); // 객체 검증 (부분적 프로퍼티 일치시 성공, 일치하지 않는게 있으면 실패)
toHaveProperty(); // 특정 프로퍼티 존재 여부 검증
objectContaining(); // 객체 내 객체 검증 (대상의 포로퍼티가 기댓값인 객체와 부분적 일치시 성공)
```

### 비동기 처리 테스트

#### Promise 반환

Promise를 반환하면서 then에 전달할 함수에 단언문 작성하는 방법

wait 함수를 실행하면 Promise instance가 생성된다
해당 인스턴스를 테스트 함수의 반환값으로 return하면, Promise가 처리 중인 작업이 완료될 때까지 테스트 판정을 유예한다

```typescript
test("지정 시간 기다리고 경과 시간과 함께 resolve", () => {
  return wait(50).then((duration) => {
    expect(duration).toBe(50);
  });
});
```

아니면
resolves 매처를 사용하는 단언문을 return
wait 함수가 resolve됐을 때의 값을 검증하고 싶다면, 첫 번째 보다 간편

```typescript
test("지정 시간 기다리고 경과 시간과 함께 resolve", () => {
  return expect(wait(50)).resolves.toBe(50);
});
```

#### async/await 활용
async 함수로 만들고, 함수 내에서 Promise가 완료될 때까지 기다리는 방법
resolves매처를 사용하는 단언문도 await로 대기시킬 수 있다 

```typescript
test("지정 시간 기다리고 경과 시간과 함께 resolve", async() => {
    await expect(wait(50)).resolves.toBe(50);
});
```
아니면 

Promise가 완료되는 것을 기다린 뒤 단언문을 실행하는 것 (가장 간략)
```typescript
test("지정 시간 기다리고 경과 시간과 함께 resolve", async () => {
  expect(await wait(50)).toBe(50);
});
```

> async/await는 비동기 처리가 포함된 단언문이 여럿일 때 한 개의 테스트 함수 내에서 정리할 수 있는 장점도 있다

#### Reject 검증 테스트

##### Promise를 return하는 방법
catch method에 전달할 함수에 단언문을 작성한다

```typescript
test("지정 시간 기다리고 경과 시간과 함께 reject된다", () => {
  return timeout(50).catch((duration) => {
    expect(duration).toBe(50);
  });
});
```

##### reject 매처를 사용하는 단언문 활용
단언문을 return하거나 async/await 사용

```typescript
test("지정 시간 기다리고 경과 시간과 함께 reject된다", () => {
  return expect(timeout(50)).rejects.toBe(50);
});

test("지정 시간 기다리고 경과 시간과 함께 reject된다", async () => {
  await expect(timeout(50)).rejects.toBe(50);
});

```

###### try-catch 사용하는 방법
Unhandled Rejection을 try 블록에서 발생시키고
오류를 catch 블록에서 받아 단언문으로 검증

#### 테스트 결과가 기댓값과 일치하는지 확인

```typescript
test("지정 시간을 기다린 뒤 경과 시간과 함께 reject된다", async () => {
  try {
    await timeout(50);  // 오류 없음 으로 종료, 테스트 성공
  } catch (err){
    expect(err).toBe(50); // 단언문은 실행되지 않는다
  }
});
```
실행하고 싶은 단언문에 도달하지 못한 채로 성공하며 종료되는데
이런 실수를 하지 않으려면 expect.assertions을 호출해야 한다.

```typescript
expect.assertions // 실행되야 하는 단언문의 횟수를 인수로 받아 기대한 횟수만큼 단언문이 호출됐는지 검증
```

```typescript
test("지정 시간을 기다린 뒤 경과 시간과 함께 reject된다", async () => {
  expect.assertions(1); // 단언문이 한 번 실행되는 것을 기대하는 테스트
  try {
    await timeout(50);  // 단언문이 한번도 실행되지 않은채로 종료, 테스트 실패
  } catch (err) {
    expect(err).toBe(50); // 단언문은 실행되지 않는다
  }
});
```

> 비동기 처리 테스트할때 첫 줄에 ```expect.assertions``` 추가시 실수 줄일수 있다 

> resolves 나 rejects 매처를 사용할 때는 주의 해야한다

예로는 wait 함수는 2000밀리초를 기다리면 2000을 반환하는 함수이기에 실패할듯 하지만, 실제로 단언문이 한 번도 평가되지 않고 종료되어, 테스트가 성공한다.

```typescript
test("return하고 있지 않기에 Promise가 완료되기전에 테스트가 종료된다", () => {
  // 실패를 기대
  expect(wait(2000)).resolves.toBe(3000);
  // 고치려면 return 앞에 넣어야한다
})
```

> synchronous function 이라면 반드시 return 해야한다.

원칙을 세우고 접근하는게 좋다

- 비동기 처리가 포함된 부분은 테스트 함수를 async함수로 만든다
- .resolves 나 .rejects가 포함된 단언문은 await한다
- try-catch 문의 예외 발생을 검증할 때는 expect.assertions을 사용한다
