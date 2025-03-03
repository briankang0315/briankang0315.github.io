---
title: "리액트 스터디 [React Route Dom] (항해99 사전스터디)"
date: 2025-03-01 20:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---

요즘 과제랑 careers fair다녔는데 몸까지 아파서 정말 너무 정신없었네요.. 이제 드디어 한 9일만에 몸상태 90%까지 왔습니다..

하아아아 다음주 금요일에 꼭 가고싶은 회사의 직책을 맡고계신 한국분이랑 회사에서 커피챗이 성사되어서 너무 떨립니다..! 

케이스 스터디 준비해 가려고 지금 학교 수업 6개 (과제 4개), 스터디 3개, 회사일, 케이스 스터디 까지 하는데 진짜 어떻게 살고 있는지 모르겠습니다

운동하는게 그렇게 싫었는데, 그 한시간이 얼마 좋은짘ㅋ.. 언제 쉴까요.. 스터디 하나 ㅃ ㅏ 져 야 ㅎ ㅏ ㄴ..

React Router Dom 입니다.

---

라우팅은 네트워크에서 path를 선택하는 프로세스를 의미합니다

## React-Router의 사용 이유

SPA의 부드러운 화면 전환을 시켜줍니다, 

## Router 종류

### HashRouter
URL의 해쉬 값을 이용하는 라우터.

주소에 해쉬가 붙고, SEO가 불가능, 새로고침시 오류가 발생하지 않는다 (그건 라우팅 사실을 서버가 몰라서..), history API를 사용하지 않기에 dynamic page에 불리하다

### BrowserRouter

history API를 사용하고, 별도 서버 설정이 없다면 새로고침 404 error, 큰 프로젝트에 적합

## React-Router-Dom

> 리액트로 만들어진 SPA 내부에서 페이지 이동이 가능하도록 만들어주는 라이브러리

### 기본 설정

```typescript
npm install react-router-dom
```

tanstack 처럼, application 최상단에 감싸야 합니다

```typescript
import {BrowserRouter} from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path = "/" element = {<Home/>}/>
        <Route path = "/about" element = {<About/>}/>
      </Routes>
    </BrowserRouter>
```

### 주요 컴포넌트 및 훅
BrowserRouter & Routes & Route

- BrowserRouter: HTML5 history API를 사용하여 URL을 관리하는 기본 라우터.
- Routes: 여러 개의 Route를 감싸는 컨테이너.
- Route: 특정 경로(path)에 대해 렌더링할 컴포넌트를 지정.

```typescript
<Route path = "*" element = {<NotFound/>}/>
```
path="*": 없는 경로로 접근했을 때 NotFound 컴포넌트 렌더링.

### Link & NavLink
Link: `<a>` 태그를 대체하여 페이지 새로고침 없이 경로를 변경.

NavLink: 현재 경로와 일치하는 경우 특정 스타일을 적용할 수 있는 링크.

```typescript
import { Link, NavLink } from "react-router-dom";

function Navbar() {
  return (
    <nav>
      <Link to="/">홈</Link>
      <NavLink to="/about" className={({ isActive }) => (isActive ? "active" : "")}>
        소개
      </NavLink>
    </nav>
  );
}
```
> NavLink의 isActive를 활용하여 현재 경로에 따라 스타일을 변경할 수 있음.

### useNavigate

페이지 이동을 할 때 사용

```typescript
import { useNavigate } from "react-router-dom";

function Home() {
  const navigate = useNavigate();

  return (
    <div>
      <h1>홈 페이지</h1>
      <button onClick={() => navigate("/about")}>소개 페이지로 이동</button>
    </div>
  );
}
```
### useParams
URL parameter 값을 가져올 때 사용

```typescript
import { useParams } from "react-router-dom";

function UserProfile() {
  const { id } = useParams(); // URL에서 id 값을 가져옴

  return <h1>사용자 ID: {id}</h1>;
}
```

라우팅 설정:
```typescript
<Routes>
  <Route path="/user/:id" element={<UserProfile />} />
</Routes>

```
/user/123 → "사용자 ID: 123" 출력

### useSearchParams
쿼리 스트링 값을 가져오거나 설정할 때 사용.

```typescript
import { useSearchParams } from "react-router-dom";

function SearchPage() {
  const [searchParams, setSearchParams] = useSearchParams();

  const keyword = searchParams.get("q"); // ?q=react

  return (
    <div>
      <h1>검색 결과: {keyword}</h1>
      <button onClick={() => setSearchParams({ q: "nextjs" })}>Next.js 검색</button>
    </div>
  );
}
```

### 중첩 라우트 (Nested Routes)
부모 컴포넌트 내부에서 자식 컴포넌트가 렌더링될 수 있도록 설정.
```typescript
<Routes>
  <Route path="/dashboard" element={<Dashboard />}>
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>
```
- dashboard/settings 접근 시 Dashboard와 Settings 둘 다 렌더링됨.
- 부모 컴포넌트에서 <Outlet />을 사용해야 함.

```typescript
import {Outlet} from "react-router-dom";

function Dashboard() {
  return (
    <div>
      <h1>대시보드 < /h1>
    < Outlet / > {/* 여기에 자식 라우트가 렌더링됨 */}
    < /div>
  );
}
```

### useLocation
현재 URL 정보 가져오기.
```typescript

import { useLocation } from "react-router-dom";

function LocationExample() {
  const location = useLocation();

  return <h1>현재 경로: {location.pathname}</h1>;
}
```
### useNavigate로 이전 페이지 이동

```typescript

function GoBackButton() {
  const navigate = useNavigate();
  return <button onClick={() => navigate(-1)}>뒤로 가기</button>;
}
```

#### Protected Route (인증 라우팅)
로그인 여부에 따라 특정 페이지 접근을 제한.

```typescript
import {Navigate} from "react-router-dom";

function ProtectedRoute({isAuthenticated, children}) {
  return isAuthenticated ? children : <Navigate to = "/login" / >;
}
```
적용:
```typescript
<Route path="/dashboard" element={<ProtectedRoute isAuthenticated={isLoggedIn}><Dashboard /></ProtectedRoute>} />
```
#### 404 페이지 처리
```typescript
<Route path="*" element={<NotFound />} />
```
또는 useRouteError()를 활용할 수도 있음.
