---
title: "러닝 타입스크립트 파트 2: 특징 [챕터 6] (항해99 사전스터디)"
date: 2025-02-20 15:00:00 +1100
categories: [ Programming, Learning Typescript ]
tags: [ Typescript, 항해99 ]
author: <author_id>   
---

TS는 초기 배열에 어떤 data types이 있는지 기억하고, 배열이 해당 타입들에서만 작동하도록 제한합니다.
이렇게, 데이터 타입을 하나로 유지시킵니다.

```typescript
const warriors = ["Artemisia", "Boudica"];

warriors.push("Zenobia") // Ok  "Zenobia" is of type string

warrior.push(true) // Error, boolean not assignable to string
```
이것 또한 마찬가지로 값이 할당되는 방식에서 코드에 의도된 타입을 이해하려고 시도하며 배열도 예외는 아닙니다.

## 배열 타입

초깃값은 필요없고, 변수는 undefined로 시작해서 나중에 값을 받을 수 있습니다.

TS는 변수에 타입 annotation을 제공해, 배열 값의 타입을 알려줍니다.
배열의 타입 annotation은 요소 타입 후 [] 붙습니다

`let arrayOfNumbers: number[];`

### 배열과 함수 타입

```typescript
// string 배열을 return 하는 함수
let createStrings: () => string[];

// 각각의 string을 반환하는 함수
let stringCreators: (() => string)[];
```

둘은 동일하지 않습니다. 주의하세요 구별해야 합니다

### 유니언 타입 배열
배열의 요소가 여러 타입중 하나일수 있음은 유니언 타입을 씁니다

물론 어느부분이 유니언인지 ()를 써야할 수도 있습니다
```typescript
// type is string or array of number
let stringOrArrayOfNumbers: string | number[];

// type is string array or number array
let arrayOfStringOrNumbers: (string | number)[];
```

```typescript
const namesMaybe = [
  "Aqaultune",
  "Blenda",
  undefined,
];
``` 

string값과 undefined값을 모두 가지므로 `(string | undefined)[]` 타입이겠죠.

### any 배열의 진화 

타입 애너테이션을 포함하지 않으면, any[]로 취급하고 모든걸 받을 수 있습니다.
하지만 이건 잠재적으로 잘못된 값의 추가, 검사기의 이점도 무력화시켜서 좋지 않습니다

### Multi-dimensional Array

2 차원 배열 또는 배열의 배열은 [][]를 갖습니다

```typescript
let arrayOfArraysOfNumbers: number[][];

arrayofArraysOfNumbers = [
  [1,2,3],
  [2,4,6],
  [3,6,9],
];
```

얘는 그래도 number[][], (number[])[]로 나타낼 수 있습니다

## 배열 멤버

인덱스 기반 접근 방식을 이해하는 언어입니다

```typescript
const defenders = ["Clarenza", "Dina"];

const defender = defenders[0]; // type is string

```

유니언 타입으로 된 배열의 멤버는 그 자체로 동일한 유니언 타입입니다 
```typescript
const soldiersOrDates = ["Deborah", new Date(1782, 6, 3)];

// type: string | Date
const soldierOrDate = soldiersOrDates[0];
```

### 주의: 불안정한 멤버

배열은 특히 타입 시스템에서 불안정 합니다.
JS에서도 조차 배열의 길이보다 큰 인덱스로 접근하면 undefined를 제공합니다

## 스프레드와 나머지 매개변수

### 스프레드

... 연산자를 이용해 배열을 결합합니다.
그럼 TS는 입력된 배열 중 하나의 값이 결과 배열에 포함될 것임을 이해합니다.

입력된 배열이 동일한 타입이라면, 출력 타입도 동일합니다

서로 다른 타입의 두 배열을 함께 스프레드해 새 배열을 생성하면, 새 배열은 유니언 타입으로 이해합니다

```typescript
const soliders = ["Harriet Tubman", "Joan of Arc", "Khutulun"];

const soldierAges = [90, 19, 45];

const conjoined = [...soldiers, ...soldierAges]; // (string | number)[]
```

### 매개변수 스프레드

TS는 나머지 arguments (매개변수)로 배열을 스프레드하는 JS 실행을 인식하고, 이에대해 타입 검사를 수행합니다

나머지 매개변수를 위한 인수로 사용되는 배열은 나머지 매개변수와 동일한 배열 타입을 가져야합니다

```typescript
function logWarriors(greeting: string, ...names: string[]) {
  for (const name of names) {
    console.log(`${greeting}, ${name}!`);
  }
}

const warriors = ["Cathay", "Lozen", "Nzinga"];

logWarriors("Hello", ...warriors);

const birthYears = [1844, 1840, 1583];

logWarriors("Hello", ...birthYears); // Error: number is not assignable to string
```

## Tuple
배열은 이론상 어떤 크기도 될 수 있습니다만, tuple이라고 하는 고정된 크기의 배열을 쓰는게 유용합니다.

튜플 배열은 각 index에 알려진 특정 타입을 가지며, 배열의 모든 가능한 멤버를 갖는 유니언 타입보다 구체적입니다.

선언 구문은 배열 literal처럼 보이지만 요소의 값 대신 타입을 적습니다 

`let yearAndWarrior: [number, string]`
index[0]에 number type, index[1]에 string값을 갖는 튜플타입을 가지고 있는 배열입니다. 

단일 조건을 기반으로 2개의 변수에 초깃값 설정하는것 처럼, 한번에 여러 값을 할당하기 위해 tuple과 array destructuring (배열 구조 분해 할당)을 함께 자주 사용합니다.

```typescript
let [year, warrior] = Math.random() > 0.5
    ? [340, "Archidamia"]
    : [1828, "Rani of Jhansi"];
```

### 튜플 할당 가능성
tuple type은 variable length (가변 길이)의 배열 타입보다 더 구체적으로 처리됩니다.
즉, 가변 길이의 배열 타입은 튜플 타입에 할당할 수 없습니다.

```typescript
const pairLoose = [false, 123] // : (boolean | number)[]
const pairTupleLoose: [boolean, number] = pairLoose; // Error, (boolean | number)[] is not assignable to [boolean, number]
// Target requires 2 element(s) but source may have fewer
```

pairLoose가 자체로 `[boolean, number]`로 선언되었으면 허용되었겠지만, 
TS는 저 튜플 타입의 튜플에 얼마나 많은 멤버가 있는지 알고 있기에, 길이가 다른 튜플로 인식해 서로 할당할 수 없습니다.

나머지 매개변수로서의 튜플

튜플은 구체적인 길이와 요소 타입 정보를 가지는 배열로 간주되므로, 함수에 전달할 인수 저장에 유용합니다.

... 로 전달된 튜플에 정확한 타입 검사를 제공합니다.

```typescript
function logPair(name: string, value: number) {
  console.log(`${name} has ${value}`);
}
const pairArray = ["Amage", 1];
logPair(...pairArray); // Error, spread arg must have a tuple type or be passed to a rest para.

const pairTupleIncorrect: [number, string] = [1, "Amage"];
logPair(...pairTupleIncorrect); // Error, type number is not assignable to string

const pairTupleCorrect: [string, number] = ["Amage", 1];
logPair(...pairTupleCorrect); // Ok
```
이렇듯, 동일한 타입이며, 순서도 같은 튜플타입이라고 알고 있다면, 사용가능 합니다.

나머지 매개변수 튜플을 사용하고 싶다면, 여러번 함수를 호출하는 인수 목록을 배열에 저장, 함께 사용가능합니다.

```typescript
function logTrio(name: string, value: [number, boolean]) {
  console.log('${name} has ${value[0]} (${value[1]})');
}

const trios: [string, [number, boolean]][] = [
  ["Aman", [1, true]],
  ["Thelf",[2, false]],
  ["Ann", [3, false]]
];

trios.forEach(trio => logTrio(...trio)); // Ok

trios.forEach(logTrio);
// Error arg of type (name: string, value: [number, boolean]) => void is not assignable to para 
// type (value: [string, [number, boolean]], ...) => void
// Types of para name and value are incompatible
// Type [string, [number, boolean]] is not assignable to string
```
trios 는 tuple array, 각 tuple은 2번째 멤버로 또 tuple 있습니다
`trios.forEach(trio => logTrio(...trio))` 는 각 ...trio가 logTrio의 매개변수 타입과 일치하므로 안전한 것으로 알려집니다
그러나 trio.forEach(logTrio)는 첫번째 매개변수로 타입이 string인 `[string, [number, boolean]]` 전체를 전달하려 하기에 할당할 수 없습니다.

### 튜플 추론
TS는 생성된 array 를 tuple이 아닌 가변 길이의 배열로 취급합니다.
배열이 변수의 초깃값 또는 함수에 대한 반환값으로 사용되는 경우, 고정된 크기의 튜플이 아니라 유연한 크기의 배열로 가정합니다.

```typescript
function firstCharAndSize(input: string){
  return [input[0], input.length];
} // return type : (string | number)[]

const [firstChar, size] = firstCharAndSize("Gudit"); // firstChar: string | number, size : string | number
```

값이 일반적인 배열 타입 대신 좀더 구체적인 tuple type이어야 함을 2가지 방법으로 나타냅니다

#### 명시적 튜플 타입 
tuple도 type annotation에 사용가능합니다
튜플 타입을 반환한다고 선언되고, 배열 리터럴을 반환한다면, 해당 배열 리터럴은 일반적인 가변 길이의 배열 대신 튜플로 간주됩니다

```typescript
function firstCharAndSizeExplicit(input: string): [string, number] {
  return [input[0], input.length];
}
```

#### const assertion
값 뒤에 넣을수 있는 const, as const연산자
TS에 타입을 유추할때 read-only가 가능한 값 형식을 사용하도록 지시합니다

```typescript
const unionArray = [1157, "Tomoe"]; // : (string | number)[]

const readonlyTuple = [1157, "Tomoe"] as const; // : readonly [1157, "Tomoe"]
```

유연한 크기의 배열을 고정된 크기의 튜플로 전환도 하지만
해당 튜플이 읽기 전용, 값 수정을 할 수 없음을 나타냅니다.

이건 함수 반환에 편리합니다, 튜플을 반환하는 함수로부터 반환된 값은 보통 즉시 구조화 되지 않으므로, 읽기 전용인 튜플은
함수를 사용하는데 방해가 되지 않습니다.

