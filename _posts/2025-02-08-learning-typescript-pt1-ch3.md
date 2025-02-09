---
title: "러닝 타입스크립트 파트 1: 개념 [챕터 3] (항해99 사전스터디)"
date: 2025-02-09 20:00:00 +1100
categories: [ Programming, Learning Typescript ]
tags: [ Typescript, 항해99 ]
author: <author_id>   
---

어제는 본가에서의 마지막 밤이었기에 공부할 시간이 없어서 오늘 공항에서 게이트 열리기를 기다리며, 비행기 안에서 스터디를 진행해 보겠습니다. (두근두근)

들어가기전, 두개의 개념의 대한 소개를 하고 가겠습니다.

1. 유니언 (union): 값에 허용된 타입을 두 개 이상의 가능한 타입으로 확장하는 것
2. 내로잉 (narrowing): 값에 허용된 타입을 하나 이상의 타입이 되지 않도록 좁히는 것

### 유니언 타입

이것 혹은 저것, 정확히는 어떤 타입인지는 모르지만, 2개 이상 옵션 중 하나라는 것을 알면 코드를 처리하는 훌륭한 개념

```|``` 연산자로 나타냅니다.

#### 유니언 타입 선언 

변수의 초깃값이 있더라도 변수에 대한 명시적 타입 애너테이션을 제공하는 것이 유용할 때 유니언 타입을 사용합니다.
당장은 null 이지만, 잠재적으로 string이 될 수 도 있다 라는 케이스가 있습니다.
```typescript
let thinker: string | null = null;

if (Math.random() > 0.5){
  thinker = "Brian"; // Ok
}
```

타입 annotation 으로 타입을 정의하는 모든 곳에서 사용 가능합니다

#### 유니언 타입 속성

값이 유니언 타입일시, TS는 유니언으로 선언한 모든 가능한 타입에 존재하는 멤버 속성에만 접근 할 수 있습니다.

그 외의 타입에 접근하려고 하면 <font color="coral">타입 검사 오류</font>가 발생합니다.

예를 들어 
```typescript
let physicist: number | string;
```
일 경우, toString()는 두 개 의 타입 (number, string)에 존재하기에 사용할 수 있지만,
toUpperCase()는 number에 없고, toFixed()는 string에 없어, 사용할 수 없습니다.

이렇듯, 모든 타입에 존재하지 않는 속성에 대한 접근을 제한하는 것은 안전 조치입니다.
객체가 어떤 속성을 포함한 타입으로 확실하게 알려지지 않은 경우, TS는 해당 속성을 사용하려는것이 안전하지 않다고 여깁니다, 존재하지 않을 수도 있으니까요.


### 내로잉
> 유니언 타입으로 정의된 여러 타입 중 하나의 타입으로 된 값의 속성을 사용하고 싶다

면, specific type중 하나라는 것을 TS에 알려야하는데, 이 과정이 narrowing이라고 부릅니다. 

내로잉이란, 유추 된것보다 더 구체적인 타입임을 코드에서 유추하는 것입니다.

타입이 더 좁혀졌다는 것을 알게 되면, 값을 더 구체적인 타입으로 취급합니다.
<font color ="coral">(타입을 좁히는 논리적 검사를 type guard라고 합니다.)</font>

#### 값 할당을 통한 내로잉
```typescript
let admiral: number | string;

admiral = "Kang"

admiral.toUpperCase(); // Ok: string

admiral.toFixed(); // Error: Property 'toFixed' does not exist on type 'string'
```

이런식으로, 초깃값이 주어질 때, 값 할당 내로잉이 작동합니다.

#### 조건 검사를 통한 내로잉

if 문을 통해 변수의 값을 좁히는 방법입니다

```typescript
let scientist = Math.random() > 0.5 
    ? "Brian"
    : 51;

if (scientist === "Brian"){ // scientist:string
  scientist.toUpperCase(); // Ok
}

scientist.toUpperCase(); //scientist:number | string Error.
```

이러한 조건부 타입 검사는, 훌륭한 JS 코딩 패턴을 미러링해 구현합니다, 변수가 여러 타입중 하나면, 일반적으로 필요한 타입과 관련된 검사를 원할 텐데
TS는 강제로 코드를 안전하게 작성할 수 있도록 해줍니다. (thanks ㅎ)

#### typeof 검사를 통한 내로잉

<font color="olivegrab">자주 사용하는 실용적인 방법.</font>

if 문처럼 유사하게 
```typescript
let researcher = Math.random() > 0.5
    ? "Brian"
    : 51

if (typeof researcher === "string"){
  researcher.toUpperCase(); // Ok: string
}

if (!(typeof researcher === "string")){
  researcher.toFixed(); // Ok: number
} else {
  researcher.toUpperCase(); // Ok: string
}
```

삼향 tuple 연산자를 이용해 작성할 수 있습니다

```typescript
typeof researcher === "string"
    ? researcher.toUpperCase(); // Ok: string
    : ressearcher.toFixed(); // Ok: number
```

### 리터럴 타입
구체적인 버전의 원시 타입.

``` 
const philosopher = "Hypatia"
```

philosopher는 string 타입인데, 단지 string이 아닌 "Hypatia"라는 특별한 값입니다.

원시 타입 값 중 어떤 것이 아닌 <font color="coral">특정 원싯값</font>으로 알려진 타입이 리터럴 타입입니다.
<i>literally that type 라는 말인가봐요 아님 말고</i>

변수를 const로 선언하고 직접 리터럴 값을 할당하면 TS는 해당 변수를 할당된 literal값으로 유추합니다.

각 원시 타입은 해당 타입이 가질 수 잇 모든 리터럴 값의 전체 조합으로 생각할 수 있습니다.

> 원시 타입은 해당 타입의 가능한 모든 리터럴 값의 집합.

boolean, null, undefined 외에는 number, string같은 원시 타입에는 무한한 수의 literal type이 있습니다 (당연하지.)

그러하여, Union type annotation에서는 원시와 리터럴을 섞어 사용할 수 있습니다.

```typescript
let lifespan: number | "ongoing" | "uncertain";
```

#### 리터럴 할당 가능성

서로 다른 원시 타입 (number, string같은) 서로 할당되지 못한다는 것을 보았는데, 
마찬가지로 0,1처럼 동일한 원시 타입이여도, 서로 다른 literal type은 서로 할당 불가,

```typescript
let specificallyAda:"Ada";

specificallyAda="Ada"; // Ok

specificallyAda="Byron"; // Error: Type "Byron" is not assignable to type "Ada"

let someString = "";

specificallyAda = someString; // Error: Type 'string' is not assignable to type "Ada"
```

그렇지만, 리터럴 타입은 그 값이 해당하는 원시 타입에 할당할 수 있습니다. (모든 특정 리터럴은 여전히 string이기에)

```typescript
someString = ":)"
```
":)"는 string type으로 간주된 someString 변수에 할당됩니다. (와우 쏘 파워풀 하다 💪🏻)

### 엄격한 null 검사
literal를 사용하는 유니언의 힘은 <font color="olivegrab">strict null checking</font>라 부르는 타입 시스템 영역인
'잠재적으로 정의되지 않은 undefined 값'으로 작업할 때 두드러집니다.

#### 십억 달러의 실수
null 참조의 발명으로 수많은 취약성 및 충돌이 발생했으며, 그 손해가 십억 달러의 고통과 피해를 초래했을것.

다른 언어들에서는 null 값을 사용하도록 허용하는 것을 가르키는 업계 용어.

TS compiler의 가장 유용한 옵션 중 하나인 strictNullChecks는 엄격한 null 검사를 허용합니다
이걸 끄면, 모든 타입에 
```
| null | undefined
```
를 추가 해야만, 모든 변수가 null 또는 undefined를 할당할 수 있습니다.

너무 리스크가 크기에, 엄격한 null 검사를 활성화해서, null 또는 undefined 값으로 인한 오류로부터 안전한지 여부를 쉽게 파악할 수 있습니다.

#### 참 검사를 통한 내로잉

```false, 0, -0, 0n, "", null, undefined, NaN``` 처럼 falsy로 정의된 값을 제외한 모든 값은 참입니다

TS는 잠재적인 값 중, truthy로 확인된 일부에 한해서만 변수의 타입을 좁힐 수 있습니다.

```typescript
let geneticist = Math.random() > 0.5
    ? "Brianna"
    : undefined;

if (geneticist){
    geneticist.toUpperCase(); // OK: string
}

geneticist.toUpperCase();
```
undefined는 항상 falsy이므로 TS는 if문의 코드 블록에서는 geneticist가 string 타입이 되어야 한다고 추론할 수 잇습니다.


논리 연산자인 &&와 ?는 참 여부를 검사하는 일도 잘 수행합니다
하지만 참 여부 말고는 암것도 안합니다.

```typescript
geneticist && geneticist.toUpperCase(); // Ok: string | undefined
geneticist.toUpperCase(); // Ok: string | undefined
```

이 예시에서, biologist는 false | string 타입이고, if 문에서는 string으로 좁힐 수 있지만, else 문에서 biologist가 빈 문자열인 경우에는 여전히 string이 될 수 있음을 알 수 있습니다.

```typescript
let biologist = Math.random() > 0.5 && "Brian";

if (bilogist){ 
  biologist; // string
} else {
  biologist; // false | string
}
```

#### 초깃값이 없는 변수

TS는 값이 할당될 때까지 변수가 undefined임을 이해할 만큼 충분히 영리합니다.
(JS는 바보)

값이 할당되기 전에 속성 중 하나에 접근하려는것 처럼 해당 변수를 사용하려고 시도하면

```typescript
let mathematician: string;

mathematician?.length // Error: Variable "mathematician" is used before being assigned

mathematician = "Mark";
mathematician.length; // Ok
```

하지만, 이럴때 ``` let mathematician: string | undefined ``` 로 declare 해주면, 오류가 보고되지 않습니다

### 타입 별칭
긴 형태의 유니언 타입을 발견할 수 있습니다.
그럴땐, 재사용하는 타입에 더 쉬운 이름을 할당해주는, <font color="olivegrab">type alias</font> 타입 별칭이 있습니다.

<b>type 새로운 이름 = 타입</b> PascalCase로 이름을 지정합니다

```typescript
type MyName = ...;
```

TS가 type alias를 발견하면, 해당 별칭이 참조하는 실제 타입을 입력한것 처럼 작동합니다.

```typescript
type RawData = boolean | number | string | null | undefined;

let rawData1: RawData;
let rawData2: RawData;
let rawData3: RawData;
```

> 타입 별칭은 JS가 아닙니다, 컴파일 되지 않습니다 오직 개발 시에만 존재 합니다.

결합도 가능합니다

```typescript
type Id = number | string;

type IdMaybe = Id | undefined | null;
```

사용 순서대로 타입 별칭을 선언할 필요는 없습니다.
파일 내에서 타입 별칭을 먼저 선언하고 참조할 타입 별칭을 나중에 선언해도 됩니다.

따라서 IdMaybe가 Id 앞으로 오도록 작성할 수 있습니다.

