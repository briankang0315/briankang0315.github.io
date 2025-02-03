---
title: "프런트엔드 개발을 위한 테스트 입문: 챕터 2 (항해99 사전스터디)"
date: 2025-02-03 12:00:00 +0800
categories: [ Programming, Frontend Testing ]
tags: [ Testing, 항해99 ]
author: <author_id>   
---

생각보다 프론트엔드 테스팅을 하는 방법은 많더라고요, 
좀 시간이 걸리겠지만
하나 확실한거는 장기적으로 봤을때는 이득이고, 코드 퀄리티도 올라가고, 시간도 따지고 보면 많이 세이브 되는거 같네요.

정말로 이번 스터디를 하는중 테스트 코드도 작성해보고, 이 교재에서 제공해주는 테스트 코드도 한번 사용해보도록 해야겠습니다 💪🏻

중요도랑 장점도 알았으니 테스트의 범위와 목적을 확실히 이해해 보겠습니다. 꼭 이해해야 자동화의 장점도 누릴 수 있다네요.

---

### 테스트 범위

웹 애플리케이션은 여러 모듈을 조합해 만든다
1. 라이브러리 제공 함수
2. 로직 담당 함수
3. UI 관련 함수
4. 웹 API 클라이언트
5. API 서버
6. DB 서버

그러므로, 테스트 작성을 할때 어디서부터 어디까지 커버하는지 명확해야한다

#### <font color="cornflowerblue"><b>정적 분석</b></font>
TS나 ESLint가 제공하는 기능을 활용하는데 <font color="lightcoral">각 모듈 내부의 검증</font>뿐만 아니라 2~3 검증, 3~4 검증처럼 <font color="lightcoral">인접 모듈을 연계</font>해 사용할 때의 문제점도 검증한다

Typescript를 활용하는 static analysis는 버그를 조기에 발견하게 해준다. type inference는 런타임 작동을 예측해줘서 유용하다. 예로서는 if 문 분기에 타입 추론을 적용되면 값을 안전하게 다룰 수 있다.

```typescript
function getMessage(name: string | undefined) {
  const a = name; // a: string | undefined
  if (!name) {
    return 'Hello anonymous';
  } // if 문 분기와 return으로 undefined가 아니라는 것이 판정된다.
  const b = name; // b: string
  return `Hello ${name}`;
}
```
함수의 return값이 생각한 타입과 일치하는지 검증할 때도 도움이 된다.

예외 발생 처리 유무에 따라 함수의 반환 타입 추론이 바뀐다.
```typescript
//// throw new Error('invalid type')
```
<i>ESLint</i>도 정적 분석 도구 중 하나로, 부적절한 구문을 수정해서 잠재적 버그를 사전에 방지합니다. 


#### <font color="cornflowerblue"><b>단위 테스트</b></font>
<font color="lightcoral">한 가지 모듈의 기능을 검증</font>하는 테스트, 독립된 환경에서 검증하기에, <font color="lightcoral">corner case</font> 검증에 적합

가장 기초적인 테스트로서, 테스트할 모듈이 특정 입력값을 받아 기대하는 출력값을 반환하는지 보는 테스트.
SPA에선 확실히 쉽다. Props(입력값)에 알맞은 출력값(HTML 블록)을 반환하기 때문에 일반적인 함수와 같은 방법으로 테스트가 가능하다.

> '이런 상황은 일어날 수 없을까?' '일어날 수 있다면 어떻게 처리해야 하는가'

같은 검토 과정에서 미쳐 고려하지 못했던 부분, 코너 케이스 (edge case라 하지 않나..?)에 한해서만 예외를 발생시키는 것이 좋은 테스트 방법일 수 있다.

#### <font color="cornflowerblue"><b>통합 테스트</b></font>
1-4, 2-3처럼 <font color="lightcoral">모듈 조합으로 제공되는 기능을 검증</font>하는 테스트, 범위가 넓어질수록 효율적일수 있지만, <font color="orange">상대적으로 대략적</font>인 검증이다.

여러 모듈을 연동한 기능을 테스트하는 integration test, 여러 모듈을 조합해 기능을 제공하며, 기능은 interaction에서 부터 시작한다.

예를 들어 목록 화면이 있는 웹 애플리케이션을 생각하면
1. select box 조작
2. URL Search query 변경
3. Search query 변경되어 data 취득 API 호출된다
4. 목록 화면 내용을 갱신한다.

select box interaction에서 시작해서 목록 화면 갱신 처리가 이뤄진다.
<font color="coral">1을 실행하면, 4도 실행된다</font> 같은 테스트가 통합 테스트이다.

1-2 만 실행되는 좁은 범위 테스트가 효율적일 때도 있다, 특히 Edge case 때문에 테스트가 복잡해지면 범위를 좁혀서 통합테스트를 해야 테스트 목적이 명확해진다.

#### <font color="cornflowerblue"><b>E2E 테스트</b></font>
1-4를 통틀어 headless browser와 UI 자동화 도구를 결함하여 검증하는 테스트, 가장 광범위한 통합 테스트, 실제로 애플리케이션을 사용할 때와 가장 유사한 테스트

UI test를 더불어 외부 스토리지와 같이 연동 중인 하위 시스템을 포함하는 테스트이다. input에 따라 저장된 값이 갱신되기에, UI는 물론 연동된 외부 기능이 정상적으로 작동하는지 검증할 수 있다.

### 테스트 목적 

테스트 타입은 <font color="lightcoral">목적에 따라 분류</font>가 됩니다
목적에 맞게 설정해야 하며, 적절한 테스트 도구가 테스트 타입마다 있습니다.
물론 여러가지를 조합해서 검증하는 경우도 있습니다.

#### <font color="olivedrab"><b>기능 테스트 Functional Test</b></font>
개발된 기능에 문제가 없는지 검증하는 테스트.
웹 프런트엔드의 기능 대부분은 UI interaction에서 시작하기에, <font color="seagreen">interaction test</font>가 기능 테스트가 될 때가 많으며, 
중요성 또한 높다. 실제 Browser API를 사용하는 것이 중요한 테스트라면, headless browser와 UI 자동화 도구를 사용하는 것이 좋다.

Interaction 테스트는 Chromium 같은 headless mode로 브라우저를 실행하여, UI 테스트 자동화 실시하는 이미지가 있는데, 리액트 같은 library로 구현된 UI 컴포넌트는 가상 브라우저 환경이 있다.

<b>실제 브라우저가 없어도 가능한 interaction test의 예시로는</b>

- 버튼 클릭시 콜백 함수 호출
- 문자 입력시 전송 버튼 활성화
- 로그아웃 버튼 클릭시 로그인 화면으로 이동

그렇지만 가상 브라우저만으로는 스크롤이나 세션 스토리지 기능 테스트를 할 수 없다. 그래서 그런 기능 테스트는 headless browser와 UI 자동화 도구를 사용한다.

<b>실제 브라우저가 필요한 interaction test 예시</b>

- 맨 아래까지 스크롤을 내리면 추가 데이터를 가져온다
- 세션 스토리지에 저장된 값을 불러온다.

#### <font color="olivedrab"><b>비기능 테스트 (접근성 테스트) Non-functional Test (Accessibility Test)</b></font>
<font color="seagreen">accessibility test</font>로서 모든 사람들이 동등하게 사용할 수 있는지 검증할 수 있는 테스트.
전 세계적으로 accessibility관련 API가 여러 플랫폼에 추가돼, 테스트 자동화를 할 때 객관적으로 검증할 수 있는 환경이 갖춰지고 있다.

키보드만으로 웹사이트를 이용할 수 있는가, contrast ratio가 시인성에 문제는 없는가 같은 검증 항목이있다..! (social informatics 관련 이네)
같은 도구를 사용하면, 초보자의 접근성의 품질을 높이는 출발점으로 삼는데 도움이 된다.

예시

 - 체크 박스를 체크할 수 있다
 - 오류 응답에 오류 문구를 렌더링한다
 - 렌더링된 화면에 접근성 위반 사례가 있는지 검사한다

![Screenshot 2025-02-03 at 4.14.12 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-03%20at%204.14.12%E2%80%AFPM.png)

#### <font color="olivedrab"><b>회귀 테스트 Regression Test</b></font>
<font color="seagreen">Regression test</font>, 특정 시점를 기준으로 전후 차이를 비교하여 문제가 있는지 검증하는 테스트. 시각적으로 보이는 UI 컴포넌트를 개발하기에,
시각적 회귀 테스트가 중요하다.

CSS는 component에 정의된 스타일 뿐만 아니라 browser에 적용된 모든 스타일에 영향을 받는다.
visual regression test (시각적 회귀 테스트)에서는 headless browser에 그려진 내용을 캡쳐하여 캡처된 이미지 간 차이를 검증한다.
초기 렌더링만이 아니고 사용자 interaction으로 변경된 화면도 한다.

예시

- 버튼 스타일에 차이가 없다.
- 메뉴 바를 열었을 때 화면에 차이가 없다.
- 렌더링된 화면에 차이가 없다.

### 테스트 전략 모델
![Screenshot 2025-02-03 at 4.19.07 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-03%20at%204.19.07%E2%80%AFPM.png)

테스트 방법들을 피라미드로 계층 분류를 해놓은 그림이다
위로 갈수록 실제 제품과 유사한 테스트가 가능하다, 하지만 많은 시간과 비용이 필요하다.
테스트용 DB server를 준비하는등, 실제 환경과 최대한 유사한 환경을 구축해야하고, 테스트할 때마다 제품과 연동된 외부 시스템의 응답도 기다려야 한다.

이런 비용은 큰 영향을 미치기에 충분한 온의가 있어야 하고, 비용 분배는 테스트 전략을 세울 떄 가장 중요한 검토 사항이다.

#### 아이스크림 콘
anti-pattern으로 자주 언급되는 테스트 모델, <font color="seagreen">상층부 테스트의 비중이 높다.</font>
외부 모듈의 의존성도 높아, 가끔 <font color="red">실패하는 불안정한 테스트가 비교적 많다</font>고 한다.

모든 테스트 통과에 수십 분 이상이 걸린다면 개발 흐름에 악영향을 주게 되고, 더 나은 개발 환경을 만들고자 자동화 해버린것이 오히려 개발 경험을 해치는 상황이 나온다.
그렇다고 실행 시간을 줄이려고 빈도를 줄이면 자동화 신뢰성은 낮아진다. 

#### 테스팅 피라미드
Mike Cohn의 저서 [경험과 사례로 풀어낸 성공하는 애자일](https://product.kyobobook.co.kr/detail/S000001032930)에서 등장한 모델로 <font color="seagreen">하층부 테스트의 비중이 높을수록</font> 더욱 안정적이고 가성비 높은 테스트가 가능하다는것이 핵심이다.

Browser를 포함하는 상층부 테스트는 실행 시간이 길어서 신속성이 떨어지는 반면, 하층부는 실행시간이 짫아 실행 빈도가 높아서 안정성도 높아진다. 

![Screenshot 2025-02-03 at 4.32.45 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-03%20at%204.32.45%E2%80%AFPM.png)

#### 테스팅 트로피
Kent C. Dodds가 만든 모델로서 <font color="seagreen">통합 테스트의 중점</font>을 두는 모델이다.

단일 UI component로 구현되는 기능은 거의 없다, 버튼을 누르면 외부 API호출하는 기능도 여러 조합으로 이루어지는것 처럼. 그리고 모든 프론트 기능은 interaction에서 시작한다. 그래서 
테스팅 트로피에는 interaction을 기점으로 한 통합 테스트 비중이 높을수록 더욱 우수하다고 주장한다.
테스팅 library와 제스트를 사용하면 headless browser없이도 interaction을 재현해 할 수 있어 실행 속도도 빠르면서 실제 제품과 유사한 테스트가 가능하다.

![Screenshot 2025-02-03 at 4.35.45 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-03%20at%204.35.45%E2%80%AFPM.png)

### 테스트 전략 기획 

#### 테스트가 없어 refactoring이 불안한 경우

테스트 코드가 없으면 리팩토링이 불안할 수 있다는건 느껴본 사실, 그럴땐 먼저 릴리스된 기능을 목록으로 정리해야 한다.
정리가 됐다면 변경 전후로 결함이 발생하지 않았는지 검증하는 회귀 테스트를 작성한다.
그러고나선 자신감있게 리팩토링이 가능하다.

웹 API server에 대한 의존성이 깔끔하게 분뢰되지 않으면 테스트 작성이 어렵다.
그래서 MOCK 서버를 활용해서 통합테스트를 실시한다. 구현 코드를 수정하지 않아도 테스트할 수 있어 리팩터링 전 테스트를 작성하고 싶은 개발자에 유용하다.
특히 이미 릴리스된 프로젝트에서 효과적이다.

통합 테스트가 늘어날수록 안심하고 리팩터링 할 수 있는 기능도 많아진다.
점진적으로 늘리면서 실시하면 더욱 안정된 테스트 피라미드 모델을 만들 수 있다

### Responsive 하게 제작된 프로젝트
모바일 까지 수정되는 상황이 발생한다 (정말 눈물납니다 이거)
반응형 웹은 테스팅 라이브러리만으로 세밀한 테스트가 어렵다. 디바이스간 다른 스타일을 제공하는 경우, 렌더링 결과를 검증할 브라우저 테스트가 필요하다.
이때 하는것이 시각적 회귀 테스트다.

스토리북은 UI 컴포넌트 단위로 시각적 회귀 테스트가 가능하다. 훨씬 효율적이다. (써봤는데 이거 재밌습니다)

### DB를 포함한 E2E Test가 필요한 경우
실제 API 서버를 사용해 E2E를 하고 싶으면 테스트용 staging environment를 사용해야 한다.
실제로 배포할 환경에 가까운 형태로 만든 테스트용 환경을 의미한다. 

테스트 엔지니어가 릴리스하기 전에 계획서를 보면서 수동으로 하기도 하고, 브라우저를 사용한 UI 자동화 방식으로 테스트하는 경우도 있다. 
물론 staging environment없이 할 수 있다. 테스트할 시스템을 컨테이너화해 CI환경에서 실행 후 연동 중인 여러 시스템과 함께 테스트하는 것이다. 
환경 구축 비용이 적고, 혼자서도 할 수 있는 장점이 있다.
![Screenshot 2025-02-03 at 4.57.36 PM.png](../assets/img/screenshots/frontend-testing/Screenshot%202025-02-03%20at%204.57.36%E2%80%AFPM.png)

그렇지만 이거 하려면 E2E framework에다 컨테이너 가상화지식, 연관 시스템 설정 지식이 필요........ 


마치며, 여러 종류의 테스트를 작성하다보면 중복이 있다, 그럴때 storybook or E2E test가 중요한 프로젝트면, UI component 테스트는 오류 패턴을 검증하는것으로도 충분하다.
테스트가 너무 많으면 과감히 줄이라. 생각보다 많이 필요하지 않다. 
