---
title: "러닝 타입스크립트 파트 1: 개념 [챕터 4] (항해99 사전스터디)"
date: 2025-02-13 13:00:00 +1100
categories: [ Programming, Learning Typescript ]
tags: [ Typescript, 항해99 ]
author: <author_id>   
---

점점 시간이 없어져 가는게 느껴지네요, 그래도 저보다 바쁘신분들 생각하면 아무것도 아니니까 더 열심히 해보도록 하겠습니다.

---

## 객체 

이번 챕터는 복잡한 객체 형태를 설명하는 방법과 타입스크립트가 객체의 할당 가능성을 확인하는 방법에 대해서 다루겠습니다.

> 객체 리터럴은 각자의 타입이 있는 키와 값의 집합입니다

### 객체 타입

{...} spread 연산자를 사용해서 객체 리터럴을 생성하면, TS는 해당 속성을 기반으로 새로운 객체 타입 또는 형태를 고려하는데

```typescript
const obj1 = {name: "Brian", age: 23};
const obj2 = {...obj1};

console.log(obj2); // { name: "Brian", age: 23 }
```

- `{...obj1}`은 obj1의 모든 속성을 새로운 객체에 복사하여 `obj2`를 생성합니다.
- 얕은 복사(Shallow Copy)이므로, `obj1`의 속성이 객체나 배열을 포함할 경우 참조를 공유합니다.
- `obj2`는 `obj1`과 같은 속성명과 값을 가지며, 원시 타입(Primitive type) 속성은 독립적으로 존재합니다.
- 객체 속성에 접근하는 방법:
  - 점 표기법(Dot notation): `obj2.name`, `obj2.age`
  - 대괄호 표기법(Bracket notation): `obj2["name"]`, `obj2["age"]`

#### (얕은 복사의 한계)

```typescript
const obj1 = {name: "Brian", details: {age: 23, city: "Seoul"}};
const obj2 = {...obj1};

obj2.details.city = "Busan";

console.log(obj1.details.city); // "Busan" (원본 객체도 변경됨!)
```

- `details`처럼 객체가 중첩된 경우, 참조를 공유하기 때문에 `obj2.details.city`를 변경하면 `obj1.details.city`도 변경됩니다.

#### 해결 방법 (깊은 복사)

```typescript
const obj1 = {name: "Brian", details: {age: 23, city: "Seoul"}};
const obj2 = structuredClone(obj1); // 깊은 복사

obj2.details.city = "Busan";

console.log(obj1.details.city); // "Seoul" (원본 유지)
```

### 객체 타입 선언
물론 객체에서 직접 타입을 유추하는 방법도 좋지만, 객체의 타입을 명시적으로 선언하는게 좋습니다.
별도로 형태를 설명하는 방법이 필요한거죠

객체 타입은 객체 리터럴과 유사하게 보이지만, 필드 값 대신 타입을 사용해 설명합니다

```typescript
let poetLater:{
  born: number;
  name: string;
};

poetLater = {born: 1935, name: "Mary Oliver",}; // Ok
poetLater = "Kang"; // Error: Type 'string' is not assignable to type `{ born: number; name: string;}'
```

#### 별칭 객체 타입
하지만 저렇게 같은 객체 타입을 계속 작성하는일은 매우 귀찮죠
각 객체 타입에 타입 별칭을 할당해 사용하는 방법이 더 일반적입니다

이건 사실 interface랑 거의 동일하다고 합니다

```typescript
type Poet = {
  born: number;
  name: string;
};

let poetLater: Poet;

poetLater = "Emily" // Error: Type 'string' is not assignable to 'Poet'
```
오류 메세지를 좀 더 읽기 쉽게 해줍니다.

### 구조적 타이핑

타입 시스템은 구조적으로 타입화 (Structually typed)되어 있습니다
즉, 타입을 충족하는 모든 값을 값으로 사용할 수 있죠.
매개변수나 변수가 특정 객체 타입으로 선언되면, 어떤 객체를 사용하든 해당 속성이 있어야 한다고 말해야 합니다
  
```typescript
type WithFirstName = {
  firstName: string;
}

type WithLastName = {
  lastName: string;
}

const hasBoth = { 
  firstName: "Kang",
  lastName: "Donghyun",
};

// Ok: 'hasBoth'는 'string' 타입의 'firstName'을 포함함
let withFirstName: WithFirstName = hasBoth; 
// Ok: 'hasBoth'는 'string'타입의 'lastName'을 포함함
let withLastName: WithLastName = hasBoth;
```

#### 사용 검사 
객체 타입으로 annotation된 위치에 값을 제공할때, TS는 값을 해당 객체 타입에 할당할 수 있는지 확인합니다.
할당하는 값에는 객체 타입의 필수속성이 다 있어야 합니다. 아니면 타입 오류

둘 사이에 일치하지 않는 타입도 허용되지 않습니다.

```typescript
type TimeRange = {
  start: Date;
};

const hasStartString: TimeRange = {
  start: '2025-02-13', // Error: Type 'string' is not assignable to type 'Date'
};
```

#### 초과 속성 검사
변수가 객체 타입으로 선언되고, 초깃값에 객체 타입에서 정의된 것보다 많은 필드가 있다면 타입 오류가 발생합니다

객체 타입으로 선언된 위치에서 생성되는 객체 리터럴에 대해서만 일어납니다. 기존 객체 리터럴을 제공하면 초과 속성 검사를 우회합니다.

> 객체 타입에 선언되지 않은 초과 속성은 종종 잘못 입력된 속성 이름이거나 사용되지 않는 코드일 수 있습니다.

#### 중첩된 객체 타입
```typescript
type Poem = {
  author : {
    firstName: string;
    lastName: string;
  };
  name: string;
};
```

이렇게 할 수 는있지만,오류 메세지에 더 많은 정보를 담으려면, author속성의 형태를 자체 별칭 객체 타입으로 추출하는게 좋습니다.

```typescript
type Author = {
  firstName: string;
  lastName: string;
};

type Poem = {
  author: Author;
  name: string;
}
```

#### 선택적 속성
물론 모든 객체에 객체 타입 속성이 필요한건 아닙니다.
```:  앞에 ?``` 추가해주면 선택적 속성임을 나태냅니다.

```typescript
type Book = {
  author?: string;
  pages: number;
}
```

주의!
- 선택적 속성과 undefined를 포함한 유니언 타입의 속성 사이에는 차이가 있습니다.
- ?를 사용해 선택적은 존재하지 않아도 되지만
- 필수로 선언된 속성과 | undefined는 그 값이 undefined여도 존재해야 합니다.

```typescript
type Writers = {
  author: string | undefined;
  editor?: string;
};

const hasRequired: Writers = {
  author: undefined, // Ok
};
```

### 객체 타입 유니언

#### 유추된 객체 타입 유니언

변수에 여러 객체 타입 중 하나가 될 수 있는 초깃값이 주어지면, 해당 타입을 객체 타입 유니언으로 유추합니다.
```typescript
const poem = Math.random() > 0.5
    ? {name: "Double Image", pages: 7}
    : {name: "Her kind", rhymes: true};

// 타입:
// { name: string; pages: number; rhymes?: undefined;}
// { name: string; pages?: undefined, rhymes: boolean}

poem.name; // string
poem.pages; // number | undefined
poem.rhymes; // boolean | undefined
```

#### 명시된 객체 타입 유니언

객체 타입의 조합을 명시하면, 객체 타입을 더 명확히 정의할 수 있습니다.
객체 타입을 더 많이 제어할 수 있다는 이점이 있습니다. 

특히 값의 타입이 객체 타입으로 구성된 유니언이라면, 모든 유니언 타입에 존재하는 속성에 대한 접근만 허용합니다.

```typescript
type PoemWithPages = {
  name: string;
  pages: number;
};

type PoemWithRhymes = {
  name: string;
  rhymes: boolean;
};

type Poem = PoemWithPages | PoemWithRhymes;

const poem: Poem = Math.random() > 0.5
  ? {name: "Double Image", pages: 7}
  : {name: "Her kind", rhymes: true};

poem.name; // Ok
poem.pages; // Error: Property 'pages' does not exist on type 'Poem'.
// Property 'pages' does not exist on type 'PoemWithRhymes'
```

에러 메세지도 참 성의것 써주는군요 

잠재적으로 존재하지 않는 객체의 멤버의 대한 접근을 제한하므로 안전을 지킵니다.

> 객체 타입 유니언도 타입을 좁혀야 합니다.

#### 객체 타입 내로잉

타입 검사기가 유니언 타입 값에 특정 속성이 포함된 경우에만 코드 영역을 실행할 수 있음을 알게 되면, 값의 타입을 해당 속성을 포함하는 구성 요소로만 좁힙니다.
즉, 코드에서 객체의 형태를 확인하고, 타입 내로잉이 객체에 적용됩니다.

```typescript
if ("pages" in poem){
  poem.pages; // Ok: poem은 PoemWithPages로 좁혀지고
} else {
  poem.rhymes; // OK: poem은 PoemWithRhymes로 좁혀집니다.
}
```

```if (poem.pages)``` 같은 형식은 안됩니다. 존재하지 않는 객체의 속성에 접근하려고 시도하는걸, 타입 가드처럼 작동하는 방식으로 사용하더라도 타입 오류가 됩니다.

#### 판별된 유니언 (discriminated union)

객체의 속성이 객체의 형태를 나태내도록 하는것
객체의 타입을 가리키는 속성이 판별값입니다

```typescript
type PoemWithPages = {
  name: string;
  pages: number;
  type: 'pages';
};

type PoemWithRhymes = {
  name: string;
  rhymes: boolean;
  type: 'rhymes';
};

type Poem = PoemWithPages | PoemWithRhymes;

const poem: Poem = Math.random() > 0.5
  ? {name: "Double Image", pages: 7, type: "pages"}
  : {name: "Her kind", rhymes: true, type: "rhymes"};

if (poem.type === "pages") { // 타입 내로잉
  console.log('It has gotten pages: ${poem.pages}'); // Ok
} else {
  console.log(`It rhymes: ${poem.rhymes}`);
}

poem.type; // 'pages' | 'rhymes'
poem.pages; // Error pages doesnt exist on Poem and PoemWithRhymes
```

### 교차 타입 (intersection type)

여러 타입임을 동시에 나타냄 
일반적으로 여러 기존 객체 타입을 별칭 객체 타입으로 결합해 새로운 타입을 생성합니다

유니언 타입과 결합할 수 있으며, 하나의 타입으로 판별된 유니언 타입을 설명하는데 유용합니다.

```typescript
type ShortPoem = {author: string} & ( 
   | {kigo: string; type: "haiku";}
   | {meter: number; type: "villanelle";}
  );


const morningGlory: ShortPoem = {
  author: "Fukuda",
  kigo: "Morning Glory",
  type: "haiku",
}; // Ok
```

#### 위험성

컴파일러나 스스로를 혼동시킬수 있습니다. 그래서 코드를 간결하게 짜야 합니다

복잡한 교차 타입을 만들게 되면 오류 메세지는 읽기 힘들어지고, 타입 검사기도 힘들어 합니다

```typescript
type ShortPoemBase = {author: string};
type Haiku = ShortPoemBase & {kigo: string; type: "haiku"};
type Villanelle = ShortPoemBase & {meter: number; type: "villanelle"};
type ShortPoem = Haiku | Villanelle;
```

이러면 이제 오류 메세지도 읽기 쉬워집니다.

#### never
교차 타입은 잘못 사용하기 쉽고, 불가능한 타입을 생성합니다
원시 타입의 값은 동시에 여러가지 타입 될수 없기에 교차 타입의 구성 요소로 함께 결합할 수 없습니다

```typescript
type NotPossible = number & string; // type: never
```

never는 bottom 또는 empty 타입을 뜻하며, 값을 가질 수 없고, 참조 할 수 없는 타입입니다
가끔 코드에서 불가능한 상태를 나타내기 위해 등장합니다.
