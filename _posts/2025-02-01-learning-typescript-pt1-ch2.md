---
title: "러닝 타입스크립트 파트 1: 개념 [챕터 2] (항해99 사전스터디)"
date: 2025-02-01 17:00:00 +0800
categories: [ Programming, Learning Typescript ]
tags: [ Typescript, 항해99 ]
author: <author_id>   
---

블로그를 사실 처음 쓰려다보니 욕심도 나고, 이쁘게 꾸미려고 하는것에 시간이 너무 뺏기는거 같네욬ㅋ

옛날 💁🏻‍♂️ 싸이월드 꾸미기가 이런느낌이었을까요.. ㅋㅋ

저번 챕터에서는 자바스크립트의 유연성이 가져오는 자유도가 초래하는 문제들,
그 문제들을 타파할 수 있는 타입스크립트의 등장, 목표, 쓰임을 알아봤습니다.

이번 챕터는 타입 시스템에 대해서 얘기를 하네요.
타입 검사기가 어떻게 작동하는지에 대해서 알아보겠습니다.

### 챕터 2

#### 2.1 타입의 종류
타입이란 자바스크립트에서 다루는 값의 <font color='lightgreen'><b>형태</b></font>에 대한 설명입니다

> 형태: 값에 존재하는 property와 method 그리고 내장되어 있는 typeof 연산자가 설명하는 것

타입스크립트는 변수의 타입을 유추할 수 있습니다

##### 원시 타입 Primitive type
- null
- undefined
- boolean 
- string
- number
- bigint
- symbol

<font color ='lightblue'>타입은 다 소문자로..!</font>

IDE에서 변수에 마우스를 올리면, 타입스크립트가 유추한 타입을 보여줍니다!
![Screenshot 2025-02-01 at 7.15.53 PM.png](../assets/img/screenshots/learning-typescript/Screenshot%202025-02-01%20at%207.15.53%E2%80%AFPM.png)

<font color ='orange'><b>타입 시스템</b></font>이란게 존재하는데, 그건 언어가 프로그램에서 가질 수 있는 타입을 이해하는 방법에 대한 규칙 집합이라네요

타입스크립트의 타입 시스템의 작동 프로세스는
1. 코드를 읽고 존재하는 모든 값과 타입을 이해하고
2. 각 값이 initialisation에서 가질 수 있는 타입을 확인하고
3. 각 값이 추후 어떻게 사용될 수 있는지 모든 방법을 확인하고
4. 값의 사용법이 타입과 일치하지 않을시 사용자에게 오류 표기하기

(바쁘다 바뻐 😓) 

예로
```typescript
let firstName = "Brian";
firstName.length();
// Error: This expression is not callable
//  Type 'Number' has no call signature
```

타입스크립트의 에러 표기 플로우는: 
1. 코드를 읽고 firstName을 이해
2. 초깃값이 "Brian"이기에 firstName이 string 타입이라고 결론짓고
3. firstName의 .length를 함수처럼 부르는 코드를 확인
4. string의 .length는 함수가 아닌 숫자라는 오류를 표시, 즉 함수처럼 호출 불가 

타입 시스템을 이해하는게 타입스크립트 코드를 이해하는데 중요하게 쓰이는 기술이라니까 저런 플로우를 계속 염두해두고 진행해봅시다.

크게 2가지 오류가 있다고 하는데

##### <font color='orangered'>구문 오류</font>
<i>자바스크립트로 변환되는 것을 차단한 경우</i>

타입스크립트가 코드로 이해할 수 없는 잘못된 구문을 감지했을때, 타입스크립트 파일에서 자바스크립트 파일을 올바르게 <font color='orangered'>생성할 수 없도록 차단</font>합니다.
물론 강제할 수는 있지만.. 결과가 상당히 다를 수 있답니다.

##### <font color='orangered'>타입 오류</font>
타입 검사기가 프로그램의 타입에서 오류를 감지했을 때 발생합니다.
물론 이건 자바스크립트로의 변환을 차단하지는 않지만, 코드가 실행되면 충돌이나 예기치 않게 작동할 수 있음을 나타냅니다.

그래서 충돌 가능성 때매 먼제 해결하라고, 실행안될 가능성이 있으니 알려주는거라네요

#### 2.2 할당 가능성
타입스크립트는 변수의 초깃값을 읽고 해당 변수가 허용되는 타입을 결정하는데, 그 변수에 새로운 값이 할당될때, 변수의 타입과 그 값이 타입이 같은지 확인하답니다.
TS (타입스크립트 줄여서)에서 함수 호출이나 변수에 값을 제공할 수 잇는지 여부를 확인하는것을 <font color='orangered'>할당 가능성(assignability)</font>라고 합니다.

> 'Type...is not assignable to type...' 형태의 오류는 가장 일반적인 타입 오류중 하나

할당 시도하려는 타입은 할당되는 변수 타입에 호환히 안됩니다.

#### 2.3 타입 애너테이션
> 근데 초깃값이 없는 경우는?

기본적으로 변수를 암묵적인 <font color='bluegreen'>`any`</font> 타입으로 간주합니다 (이거 좋은데 남발하다가 나중에는 어 이럴꺼면 TS 왜쓰지 했습니다)

초기 타입을 유추할 수 없는 변수는 <font color='green'>`진화하는 any`</font>라고 부르는데, 이게 특정 타입을 강제하는 대신 새로운 값이 할당될때마다 변수 타입을 바꿔주는게 가능.

근데 저도 느꼇지만, any는 정말 되도록이면 쓰지않아야 하는거 같습니다. 타입 검사 기능 (주요 기능중 하나)을 못하니까 쓸모없어지는거죠.

그래서 초깃값을 할당하지 않아도, 변수의 타입을 선언해야 합니다 <font color='gold'><b>(Type Annotation)</b></font>

```typescript
let name: string;
name = "Brian";
```

이 annotation기능은 TS에만 존재하며, 런타임 코드에 영향을 주지도 않고 (첨엔 느려지는줄..), 자바스크립트 구문도 아닙니다.
tsc 명령어를 실행해서 컴파일시 사라지는 코드 랍니다.

type annotation을 해서 특정 타입을 선언 해줬는데, 다른 타입인걸 할당해주면 당연히 타입 오류를 보여줍니다.

```typescript
let name: string;
name = 23;
// Error: Type 'number' is not assignable to type 'string'.
```

중복 타입 선언 얘기도 있지만, 불필요하고, 타입이 복잡하면 귀찮다고 하지만, 전 최대한 항상 선언을 해줘야한다는 주의입니다 <s>(그래놓고 다 빼먹을꺼면서)</s>
#### 2.4 타입 형태

타입스크립트는 변수에 할당된 값의 타입일치를 확인하는 것 이상을 수행합니다
TS는 객체(object)에 어떤 멤버 속성이 존재하는지 알아서, 변수의 속성을 접근하려하면, 해당 변수의 타입에 존재하는지 확인해줍니다

```typescript
let name = {
  firstName: "Brian",
  lastName: "Kang",
};

name.middleName;
// Error: Property 'middleName' does not exist on type
// '{firstName: string; lastName: string;}'
```

이렇듯, 할당 가능성뿐만 아니라, 관련된 문제도 알려줍니다. (미..미..미쳣다)

2015년에 들어와서야 import export 구문이 추가되었다네요 (와 이거 없었으면 어떻게 코드를 공유했을까.. 이런거 보면 정말 새로운게 계속 나오는데 뒤쳐지면 따라 잡는게 힘들겠구나 싶네요)

```typescript
import { value } from "./values";

export const doubled = value * 2;
```
import 나 export가 있는 파일을 <font color='orangered'><b>모듈</b></font>이라고 정의하고, <font color='green'>스크립트</font>를 모듈이 아닌 모든 파일이라고 정의 하고 가겠답니다 

export를 선언에 안붙히면, 그 파일안에서만 사용가능하고, 
다른 모듈에서 같은 변수 이름을 써도 ㅇㅋ, 어차피 import할때 각 다른 모듈에서 가져오기때문에, 하지만 그 모듈에 다시 같은 이름의 변수를 만들어버리면 안됩니다, 타입 에러 발생합니다.

하지만, 파일이 스크립트라면, 해당 파일을 전역 scope로 간주해서 모든 스크립트가 파일의 내용에 접근할 수 있습니다.
즉, 스크립트 파일에 선언된 변수는 다른 스크립트 파일에 선언된 변수와 동일한 이름을 가질 수 없습니다.

```typescript
const shared = "Kang";
// Error: Cannot redeclare block-scope variable 'shared'
```
이런 오류가 표시되면 export, import문을 추가하지 않았기 때문..!
뭐 스크립트 사양에따라 export import없이 파일을 모듈로 만들어야한다면, export{}; 를 추가해서 강제화해야한답니다.
<i>(CommonJS 같은건 import export를 인식 못한다고 하네요)</i>
