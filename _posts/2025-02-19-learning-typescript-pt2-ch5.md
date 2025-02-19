---
title: "러닝 타입스크립트 파트 2: 특징 [챕터 5] (항해99 사전스터디)"
date: 2025-02-19 21:00:00 +1100
categories: [ Programming, Learning Typescript ]
tags: [ Typescript, 항해99 ]
author: <author_id>   
---

50페이지는 불가능 한거 같습니다. 아무리 시간을 짜도, 힘드네요.

타입 annotation을 사용해 변숫값에 주석을 추가하는 법을 봣는데, 이젠
함수 매개변수와 반환 타입에서 동일한 작업을 수행하는 방법과 이 방법이 유용한 이유를 살펴 보겠답니다.

## 함수

### 함수 매개변수

명시적 타입 정보가 선언되지 않으면 절대 타입을 알 수 없고, TS는 any로 간주하여 매개변수는 어느타입이 될 수도 있습니다

```typescript
function sing(song: string){
  ...
}
```

물론, 안해도 돌아가지만, 자바스크립트로 변해져 돌아가기에, 문제가 생길 수 있으니 주의하세요.

### 필수 매개변수
TS는 함수에 선언된 모든 매개변수가 필수로 가정해주고, 그 숫자도 엄격하게 봅니다
```(first: string, second: string)``` 이렇게 2개가 필요하다 선언시, 2개만 줄 수 있습니다.

이렇게 required parameter를 제공하도록 강제하면, 타입 안정성을 강화합니다.

### 선택적 매개변수
undefined 값을 위해 의도적으로 매개변수를 제공안할 때도 있습니다.
이랬을때 타입오류를 안받고 싶다면, 타입 애너테이션 : 앞에 ?를 추가하면 됩니다

함수 호출에 optional parameter를 제공할 필요는 없고, 항상 `| undefined`가 유니언 타입으로 추가되어 있습니다,

```typescript
function announceSong(song: string, singer?: string) {
  console.log(`Song: ${song}`);

  if (singer) {
    console.log(`Singer: ${singer}`);
  }
}

announceSong("Green"); // Ok
announceSong("Green", undefined); // Ok
announceSong("Green", "Aespa") // Ok
```

암묵적으로 undefined가 될 수 있습니다
singer는 `string | undefined` 타입으로 시작 하지만 if문 덕에 string 타입으로 좁혀집니다.

선택적이 아닌 매개변수가 `| undefined`이라면, 꼭 명시적으로 제공되어야 합니다

```typescript
function announceSong(song: string, singer: string | undefined){
  ..
}

announceSong("Green", undefined) // Ok
announceSong("Green") // Error
```

> 선택적 매개변수는 항상 마지막이어야 합니다. 오류 생깁니다


### 기본 매개변수

기본값을 제공하고 선언할 수 있습니다
이러면 암묵적으로 `| undefined`도 유니언 타입이 추가됩니다.

타입 애너테이션이 없다면, 제공한 기본값을 기반으로 타입을 유추합니다.

### 나머지 매개변수

... 스프레드 연산자로 임의의 수의 인수로 호출할 수 있도록 해줍니다

```typescript
function singAll(singer: string, ...songs: string[]) {
  for (const song of songs) {
    console.log(`${song}, by ${singer}`);
  }
}

singAll("Alicia Key"); // Ok
singAll("Lady Gaga", "Bad Romance", "Just Dance", "Poker Face"); // Ok

singAll("Ella Fitzgerald", 2000) // Error number not assignable to string
```

## 반환 타입

TS는 지각적 (perceptive)입니다 
함수가 반환할 수 있는 가능한 모든 값을 이해하면, 함수 반환값의 타입도 알 수 있습니다.

여럭의 반환문을 포함한다면, 가능한 모든 탄환 타입의 조합으로 유추합니다

```typescript
function getSongAt(songs: string[], index: number){
  return index < songs.length 
      ? songs[index]
      : undefined;
} // 타입: string | undefined
```

`string | undefined`를 반환하는것으로 유추됩니다. 두가지 가능한 반환값이 각각 string과 undefined이기 때문입니다.

### 명시적 반환 타입 (explicit return)

명시적으로 선언하는게 유용할때는
- 가능한 반환값이 많은 함수가 항상 동일한 타입의 값을 반환하도록 강제합니다
- TS는 재귀 함수의 반환 타입을 통해 타입을 유추하는것을 거부
- 수백개의 파일이 있는 프젝에서 타입 검사 속도를 높일 수 있다

함수 선언 반환 타입 애너테이션은 매개변수 목록 () 다음에 옵니다.
함수 선언의 경우는 {} 앞에 배치됩니다.
화살표 함수는 => 앞에 배치됩니다.


반환 타입으로 할당할 수 없는 값을 반환하면 할당 가능성 오류를 표시합니다.

## 함수 타입

JS는 함수를 값으로 전달할 수 있습니다
함수를 가지기 위한 매개변수 또는 변수의 타입을 선언하는 방법이 필요합니다.

function type 구문은 arrow function와 유사하지만 함수 본문 대신 타입이 있습니다.

다음 nothingInGivesString 변수 타입은 매개변수가 없고 string type return 함수임을 설명합니다.

`let nothingInGivesString: () => string;`

inputAndOutput 변수 타입은 string[] 매개변수와 count 선택적, 및 number값을 반환하는 함수임을 설명합니다

`let inputAndOutput: (songs: string[], count?: number) => number;`

콜백 매개변수 (함수로 호출되는 매개변수) 설명하는데 자주 사용됩니다.

### 함수 타입 괄호

함수 타입은 다른 타입이 사용되는 모든 곳에 배치할 수 있습니다.
유니언도 포함!

유니언 타입의 annotation에서 함수 반환 위치를 나타내거나 유니언 타입을 감싸는 부분을 표시할 때 괄호를 사용합니다.

`let maybeReturnsString: (() => string) | undefined`;

### 매개변수 타입 추론

선언된 타입의 위치에 제공된 함수의 매개변수 타입을 유추할 수 있습니다.

singer변수는 string타입의 매개변수를 갖는 함수로, singer가 할당되는 함수 내의 song 매개변수는 string으로 예측됩니다.

```typescript
let singer: (song: string) => string;

singer = function (song) {
  // song: string의 타입
  return `Singing: ${song.toUpperCase()}!`; // Ok
}
```

함수를 매개변수로 갖는 함수에 인수로 전달된 함수는 해당 매개변수 타입도 잘 유추할 수 있습니다.

예로, song과 index매개변수는 각각 string, number 로 유추됩니다

```typescript
const songs = ["Call Me", "Jolene", "The Chain"];

// song: string
// index: number
songs.forEach((song, index) => {
  console.log(`${song} is at index ${index}`);
});
```

### 함수 타입 별칭

함수 타입에서도 동일하게 타입 별칭 사용 가능

```typescript
type StringToNumber = (input: string) => number;

let stringToNumber: StringToNumber;

stringToNumber = (input) => input.length; // Ok
stringToNumber = (input) => input.toUpperCase(); // Error, string not assignable to number
```

타입 별칭을 이용하면, 반복적으로 작성하는 매개변수와 반환 타입을 갖는 코드 공간을 많이 절약할 수 있습니다.

## 그 외 반환 타입

### void return type
어떤 값도 반환 하지 않고, void로 반환 값이 없는 함수의 반환 타입을 확인할 수 있습니다. 

함수에서 반환되는 모든 값은 무시되어서, 매우 유용합니다.

JS는 실젯값이 반환 안되면 undefined를 반환하지만, 이는 void와는 동일하지 않습니다.
undefined는 반환되는 리터럴 값입니다.
undefined를 포함하는 대신, void 타입의 값을 할당하려고 하면 타입 오류.

하지만, 둘이 구분해서 사용하면 매우 유용합니다.

특히 void를 반환하도록 선언된 타입 위치에 전달된 함수가 반환된 모든 값을 무시하도록 설정할때 용이

### never return type
의도적으로 항상 오류를 발생시키거나 무한 루프를 실행하는 함수 


> never 와 void는 다릅니다
> void는 아무것도 반환하지 않는 함수를 위한 것
> never는 절대 반환하지 않는 함수를 위한 것.

## 함수 오버로드 

일부 함수는 선택적, 나머지 매개 변수로만 표현할 수 없는 다른 매개변수들로 호출될 수 있습니다
이러한 함수는 overload signature라고 불립니다.
즉, 하나의 최종 implementation signature와 그 함수의 본문 앞에 서로 다른 버전의 함수 이름, 매개변수, 반환 타입을 여러 번 선언합니다.

이 함수 호출에 대해 구문 오류 생성 여부 결정은 오버로드 시그니처만 확인합니다.
(구현 시그니처는 함수의 내부 로직에서만 사용.)

> 복잡하고 설명하기 어려운 함수 타입에 사용하는 최후의 수단입니다.
> 단순하게 유지하고 가능하면 사용하지 마세요.

### 호출 시그니처 호환성

오버로드된 함수의 구현에서 사용되는 
구현 시그니처는, 매개변수 타입과 반환 타입에 사용하는것과 동일.

따라서, 오버로드 시그니처에 있는 return type과 각 매개변수는 구현 시그에 있는 동일한 인덱스의 매개변수에 할당할 수 있어야 합니다.
즉 호환되어야 합니다.

다음 format 매개변수는 string, 2개의 오버로드 시그는 string과 호환, 3번째는 () => string과 호환 안됨.

```typescript
function format(data: string): string; // Ok
function format(data: string, needle: string, haystack:string): string; // Ok

function format(getData: () => string): string; // Error, overload sig not compatible with its implementation sig.
```
