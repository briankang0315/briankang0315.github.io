---
title: "러닝 타입스크립트 파트 2: 특징 [챕터 7] (항해99 사전스터디)"
date: 2025-02-20 20:00:00 +1100
categories: [ Programming, Learning Typescript ]
tags: [ Typescript, 항해99 ]
author: <author_id>   
---

인터페이스 진행하겠습니다.

> 연관된 이름으로 객체 형태를 설명하는 다른 방법

> 일반적으로 더 읽기 쉬운 오류 메세지, 더 빠른 컴파일러 성능, 클래스, 더 나은 상호 운용성

## 타입 별칭 vs 인터페이스

`born: number`와 `name: string`을 가진 객체를 타입 별칭으로 구현

```typescript
type Poet = {
  born: number;
  name: string;
}
```

인터페이스로는
```typescript
interface Poet {
  born: number;
  name: string;
}
```
할당 가능성 오류 와 검사는 객체 타입과 거의 동일합니다.

차이점으로는 

- 속성 증가를 위해 merge할 수 있다 (내정된 전역 인터페이스나 npm package같은 외부 코드 사용시 유용)
- 인터페이스는 클래스가 선언된 구조의 타입을 확인하는데 사용가능
- 인터페이스에서 타입 검사기 작동이 더 빠름, 내부적으로 더 쉽게 cache 할 수 있는 명명된 타입 선언
- 이름있는 (명명된) 객체로 간주됨으로 어려운 특이 케이스의 오류 메세지가 쉽게 나옵니다

> 타입 별칭의 유니언 타입과 같은 기능이 필요할때까진, 인터페이스 사용이 좋습니다.

## 속성 타입

### 선택적 속성

```typescript
interface Book{
  author?: string;
  pages: number;
}
```
undefined를 포함한 union type의 선택적 속성과 일반 속성 사이의 차이점과 관련된 주의 사항은 객체 타입과 마찬가지로 인터페이스에도 적용됩니다

### 읽기 전용 속성

경우에 따라 interface 에 정의된 객체의 속성을 재할당하지 못하도록 차단하고 싶은데
속성 이름앞에 readonly 를 추가해서 다른 값으로 재설정 못하게 할수있습니다, 시도시 타입 오류

```typescript
interface Page {
  readonly text: string;
}
```
readonly modifier는 타입 시스템에만 존재하며 인터페이스에서만 사용가능 합니다.
객체의 인터페이스를 선언하는 위치에서만 사용되고 실제 객체에서는 적용되지 않습니다.

Page 예제에서 text속성의 부모 객체는 함수 내부에서 text로 명시적으로 사용되지 않았기 때문에 
함수 밖에서 속성을 수정할 수 있습니다.
 writable 속성을 readonly에 할당할 수 있으므로 pageIsh는 Page로 사용할 수 있습니다
가변 (쓰기 가능한) 속성은 readonly 속성이 필요한 모든 위치에서 읽을 수 있습니다.

pageIsh를 Page 타입으로 선언하면 text속성이 readonly라고 가리키게 됩니다. 하지만 유추된건 readonly가 아니었습니다.

readonly interface 멤버는 코드에서 객체를 의도치않게 수정하는 것을 막는 편리한 방법입니다.

그러나 readonly는 타입 시스템 구성 요소일 뿐, 컴파일된 JS에는 존재하지 않습니다.
개발 중 그 속성이 수정 되지 못하도록 보호하는 역할일 뿐입니다. 

### 함수와 메서드

인터페이스 멤버를 함수로 선언하는 두 가지 방법을 제공
- 메서드 구문: 인테페이스 멤버를 member(): void와 같이 객체의 멤버로 호출되는 함수로 선언 
- 속성 구몬: 인터페이스 멤버를 member(): () => void와 같이 독립 함수와 동일하게 선언

JS에서 객체를 함수로 선언하는 방법과 동일합니다.

```typescript
interface HasBothFunctionTypes {
  property: () => string;
  method(): string;
}

const hasBoth: HasBothFunctionTypes = {
  property: () => "",
  method(){
    return "";
  }
};

hasBoth.property(); // Ok
hasBoth.method(); // Ok
```
method와 property 멤버 둘 다 매개변수 없이 호출되어 string을 반환합니다

선택적 ? 속성으로 필수가 아닌것을 나타냅니다
```typescript
interface OptionalReadonlyFunctions {
  optionalProperty?: () => string;
  optionalMethod?(): string;
}
```

메서드와 속성선언은 교환하여 사용가능, 차이점은,

- 메서드는 readonly로 선언할 수 없지만, 속성은 가능합니다
- 인터페이스 병합은 메서드와 속성을 다르게 처리합니다
- 타입에서 수행되는 일부 작업은 메서드와 속성을 다르게 처리합니다

추천하는 스타일 가이드로서는
- 기본 함수가 this를 참조할 수 있다는것을 알고 있다면, 메서드 함수를 사용하세요
- 반대의 경우는 속성 함수를 사용하세요

### 호출 시그니처 
call signature로 선언할 수 있습니다
값을 함수처럼 호츨하는 방식입니다

호출 시그니처가 선언한 방식으로 호출되는 값만 인터페이스에 할당할 수 있습니다.
즉, 할당 가능한 매개변수와 반환 타입을 가진 함수입니다. 

호출 시그니처는 함수타입과 비슷하지만, : 대신 => 로 표시합니다
```typescript
type FunctionAlias = (input: string) => number;

interface CallSignature {
  (input: string): number;
}
// : (input: string) => number
const typedFunctionAlias: FunctionAlias = (input) => input.length; // Ok
// : (input: string) => number
const typedCallSignature: CallSignature = (input) => input.length; // Ok
```
사용자 정의 속성을 추가로 갖는 함수를 설명하는 데 사용할 수 있습니다.
TS는 함수 선언에 추가된 속성을 해당 함수 선언의 타입에 추가하는것으로 인식합니다.


## 인덱스 시그니처

객체에서 키의 타입을 미리 알 수 없을 때, 동적으로 여러 개의 속성을 가질 수 있도록 인덱스 시그니처를 사용합니다.  
이는 객체의 특정 속성 이름을 미리 정의하지 않고 유연한 형태로 다룰 수 있게 합니다.

```typescript
interface Dictionary {
  [key: string]: string;
}

const words: Dictionary = {
  hello: "안녕하세요",
  world: "세계",
};
```

### 속성과 인덱스 시그니처 혼합

인덱스 시그니처를 사용할 때, 명시적인 속성과 함께 사용할 수도 있습니다.  
단, 명시적인 속성의 타입이 인덱스 시그니처에서 허용하는 타입과 일치해야 합니다.

```typescript
interface LooseObject {
  name: string;
  [key: string]: string; // 모든 속성의 타입이 string이어야 함
}

const example: LooseObject = {
  name: "타입스크립트",
  description: "언어",
  author: "앤더스 헤일스버그"
};
```

> `name` 속성의 타입이 `string`이므로, 인덱스 시그니처도 `string`이어야 합니다.  
> 만약 `number` 같은 다른 타입을 추가하려 하면 오류가 발생합니다.


### 숫자 인덱스 시그니처

일반적인 인덱스 시그니처는 `string`을 키로 사용하지만, 숫자를 키로 사용할 수도 있습니다.  
이때, 숫자 인덱스 시그니처는 `string` 인덱스보다 더 제한적으로 동작합니다.

```typescript
interface NumericIndex {
  [index: number]: string;
}

const numericObject: NumericIndex = {
  0: "첫 번째",
  1: "두 번째",
};
```

### 문자열과 숫자 인덱스의 차이점

숫자 인덱스를 사용하면, `string` 인덱스보다 엄격한 규칙이 적용됩니다.  
특히, TS는 숫자 키를 내부적으로 문자열로 변환하여 처리하기 때문에, `string` 인덱스와 함께 사용할 때 주의해야 합니다.

```typescript
interface MixedIndex {
  [key: string]: string; // 문자열 키
  [index: number]: string; // 숫자 키
}

const example: MixedIndex = {
  1: "첫 번째", // 내부적으로 "1"로 변환됨
  key: "문자열 키",
};
```

> 숫자 인덱스를 사용할 때는 해당 속성이 `string` 인덱스와 충돌할 가능성이 있다는 점을 유의하세요.

## 중첩 인터페이스

인터페이스 내부에서 다른 인터페이스를 속성 타입으로 지정하여 중첩된 구조를 만들 수 있습니다.  
이를 통해 객체 내 객체를 명확하게 모델링할 수 있습니다.

```typescript
interface Author {
  name: string;
  birthYear: number;
}

interface Book {
  title: string;
  author: Author; // 다른 인터페이스를 속성으로 사용
}

const book: Book = {
  title: "타입스크립트 완벽 가이드",
  author: {
    name: "앤더스 헤일스버그",
    birthYear: 1960,
  },
};
```

> 중첩 인터페이스를 활용하면, 구조적인 타입을 더욱 명확하게 정의할 수 있습니다.

## 인터페이스 확장

인터페이스는 `extends` 키워드를 사용하여 확장할 수 있습니다.  
이렇게 하면 기존 인터페이스의 속성을 상속받아 추가적인 속성을 정의할 수 있습니다.

```typescript
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

const myDog: Dog = {
  name: "멍멍이",
  breed: "리트리버",
};
```

> `Dog` 인터페이스는 `Animal`을 확장하여 `name` 속성을 상속받고, `breed` 속성을 추가합니다.

### 재정의된 속성

하위 인터페이스에서 기존 속성을 더 좁은 범위의 타입으로 재정의할 수 있습니다.  
하지만 더 넓은 범위의 타입으로 확장하는 것은 불가능합니다.

```typescript
interface Base {
  id: string;
}

interface Extended extends Base {
  id: "fixed"; // 가능 (더 좁은 타입)
}

const obj: Extended = {
  id: "fixed",
};
```

> `Base`의 `id`는 `string`이지만, `Extended`에서는 `"fixed"`라는 리터럴 타입으로 재정의되었습니다.

### 다중 인터페이스 확장

인터페이스는 여러 개의 인터페이스를 동시에 확장할 수 있습니다.  
이 경우, 모든 부모 인터페이스의 속성을 상속받습니다.

```typescript
interface Shape {
  color: string;
}

interface Dimensions {
  width: number;
  height: number;
}

interface Rectangle extends Shape, Dimensions {
  area: number;
}

const rect: Rectangle = {
  color: "blue",
  width: 20,
  height: 10,
  area: 200,
};
```

> `Rectangle`은 `Shape`과 `Dimensions`를 동시에 확장하여, `color`, `width`, `height`, `area` 속성을 모두 포함합니다.

## 인터페이스 병합

같은 이름을 가진 인터페이스가 여러 번 선언되면, 자동으로 병합(Merging)됩니다.  
이렇게 하면 기존 인터페이스에 새로운 속성을 추가할 수 있습니다.

```typescript
interface User {
  name: string;
}

interface User {
  age: number;
}

const person: User = {
  name: "홍길동",
  age: 30,
};
```

> 두 개의 `User` 인터페이스가 병합되어, `name`과 `age` 속성을 모두 포함하는 인터페이스가 생성됩니다.

### 이름이 충돌되는 멤버

병합된 인터페이스에서 같은 이름의 속성이 서로 다른 타입을 가지면 오류가 발생합니다.

```typescript
interface A {
  value: string;
}

interface A {
  value: number; // 오류: 같은 속성에 서로 다른 타입을 선언할 수 없음
}
```

그러나, 메서드 오버로드(Overload)는 병합 시 허용됩니다.

```typescript
interface Printer {
  print(value: string): void;
}

interface Printer {
  print(value: number): void;
}

const myPrinter: Printer = {
  print(value) {
    console.log(value);
  },
};

myPrinter.print("Hello"); // Ok
myPrinter.print(123); // Ok
```

> 메서드는 여러 개의 선언이 있을 때, 가장 구체적인 선언을 우선적으로 사용합니다.

## 정리

인터페이스는 타입스크립트에서 객체의 구조를 정의하는 핵심적인 기능입니다.  
특히, 확장(`extends`), 병합(`interface Merging`), 인덱스 시그니처 등을 활용하면 더 유연하고 강력한 타입 설계가 가능합니다.  

- `interface`는 객체의 형태를 명확하게 정의하는 데 적합.
- 확장(`extends`)을 통해 코드 재사용성 증가.
- 병합을 이용해 외부 코드의 인터페이스를 확장 가능.
- 인덱스 시그니처를 사용해 유연한 속성 처리 가능.
