---
title: "리액트 스터디 [TanStack Query] (항해99 사전스터디)"
date: 2025-02-22 18:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---

> <b>TanStack Query(구 React Query)는 웹 애플리케이션에서 데이터 가져오기, 캐싱, 동기화 및 서버 상태 업데이트를 간편하게 처리할 수 있도록 도와주는 라이브러리입니다.</b>  
> 많은 웹 프레임워크가 데이터 가져오기에 대한 명확한 방식을 제공하지 않기 때문에, 개발자들은 비효율적인 방법으로 데이터를 관리하는 경우가 많습니다.
> TanStack Query는 이러한 문제를 해결하여, 더 나은 서버 상태 관리 경험을 제공합니다.

## <b>동기 Motivation</b>

<b>기존 방식과 비교</b>

- `useState`와 `useEffect`를 사용하여 데이터를 관리하면 <b>비효율적인 코드</b>가 많이 생김
- 데이터를 요청할 때마다 API를 다시 호출해야 하며, 캐싱이 불가능함
- 로딩 상태, 에러 처리 등을 직접 구현해야 해서 코드가 길어짐

그리하여 대부분 개발자들은

- Redux와 같은 상태 관리 라이브러리를 활용
- 비동기 데이터를 직접 전역 상태에 저장

그러나 이런 방식들은 <b>서버 상태 (Server State) 관리에 적합하지 않습니다.</b>
서버 상태는 클라이언트 상태와 다르며, 특징으로선

1. 서버 상태는 원격 저장소에 존재하며, 개발자가 직접 관리할 수 없음
2. 비동기 API를 통해 데이터를 가져오고 업데이트해야 함
3. 다른 사용자의 작업으로 인해 데이터가 예기치 않게 변경될 수 있음
4. 적절히 관리하지 않으면, 오래된 데이터를 사용할 가능성이 있음

### server-state 관리의 주요 어려움

서버 상태를 관리할 때, 개발자들은 다음과 같은 여러 가지 도전에 직면하게 됩니다.

| Challenge         | 설명                                                       |
|-------------------|----------------------------------------------------------|
| 캐싱                | 	데이터를 저장하고 재사용하여 불필요한 API 호출을 줄이는 것이 중요하지만, 제대로 구현하기 어려움 |
| 중복 요청 방지	         | 동일한 데이터를 여러 번 요청하는 경우, 중복된 네트워크 요청을 하나로 합쳐야 함            |
| 백그라운드 데이터 업데이트	   | 오래된 데이터를 자동으로 새롭게 갱신하는 기능이 필요함                           |
| 데이터가 최신인지 확인      | 	클라이언트가 사용하는 데이터가 최신 상태인지 파악하는 것은 까다로움                   |
| 빠른 UI 업데이트        | 	데이터가 변경되면 UI에 즉시 반영되어야 함                                |
| 페이징 및 지연 로딩       | 	한 번에 모든 데이터를 불러오는 것이 아니라 필요한 만큼 가져오는 방식이 필요함            |
| 메모리 관리 및 자동 정리	   | 사용하지 않는 데이터를 메모리에서 자동으로 제거해야 함                           |
| 구조적 공유를 통한 메모이제이션 | 	동일한 데이터 구조를 여러 곳에서 재사용할 수 있어야 함                         |

이 모든 문제를 개발자가 직접 해결하려면, 복잡한 로직을 구현해야 하고 유지보수도 어렵습니다.

<b>React Query를 사용하면?</b>

복잡한 상태 관리 코드 제거 <br/>
→ useEffect, useState, Redux와 같은 별도의 상태 관리 코드가 필요 없음. <br/>
데이터 가져오기, 캐싱, 업데이트 자동화 <br/>
→ useQuery와 useMutation을 사용하면 API 데이터를 자동으로 관리할 수 있음. <br/>
애플리케이션 성능 최적화 <br/>
→ 불필요한 API 호출을 줄이고, 필요한 데이터를 캐싱하여 성능을 향상시킴.<br/>
빠르고 반응성 좋은 UI 제공 <br/>
→ invalidateQueries, refetchOnWindowFocus 등의 기능을 활용해 최신 데이터를 유지. <br/>
네트워크 트래픽 절감 및 메모리 최적화 <br/>
→ 캐싱과 구조적 공유(Memoization)를 통해 중복된 데이터를 줄이고, 네트워크 요청을 최소화. <br/>
## React Query가 필요한 이유

### <b>기존 방식과의 차이점</b>

| 비교 항목             | 기존 방식 (`useState` + `useEffect`)  | React Query                           |
|-------------------|-----------------------------------|---------------------------------------|
| <b>데이터 저장</b>     | `useState`, Redux Store 등에서 직접 관리 | 자동 캐싱 및 동기화                           |
| <b>API 요청</b>     | `useEffect`와 `fetch` 사용           | `useQuery`로 간단하게 처리                   |
| <b>데이터 최신화</b>    | 변경될 때마다 직접 API 다시 호출              | 자동 리패칭 및 백그라운드 업데이트                   |
| <b>로딩 / 에러 처리</b> | `useState`로 직접 상태 관리 필요           | `isLoading`, `isError` 자동 제공          |
| <b>성능 최적화</b>     | 캐싱 없음 → 불필요한 API 호출 발생            | 자동 캐싱, `staleTime`, `cacheTime` 활용 가능 |

React Query를 사용하면 <b>API 요청을 간단하고 효율적으로 관리할 수 있음</b>

## React Query 사용법

### <b> React Query 설치</b>

```bash
npm i @tanstack/react-query
```

### <b>기본 설정 (`QueryClientProvider` 적용)</b>

React Query를 사용하려면 <b>프로젝트의 최상단에서 `QueryClientProvider`를 적용해야 합니다.</b>  
이 설정을 하지 않으면 `useQuery`와 `useMutation`을 사용할 수 없음

```tsx
import {QueryClient, QueryClientProvider} from "@tanstack/react-query";

const queryClient = new QueryClient(); // React Query의 모든 기능을 사용할 수 있게 해줌

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <MyComponent/>
    </QueryClientProvider>
  );
}
```

## useQuery 사용법 (데이터 패칭)

### <b>`useQuery`</b>

`useQuery`는 API 요청을 <b>자동으로 관리</b>하는 React Query의 핵심 기능입니다.

- <b>자동 캐싱</b>
- <b>로딩, 에러 상태 제공</b>
- <b>백그라운드 데이터 업데이트 가능</b>

### <b>예제</b>

```tsx
import {useQuery} from "@tanstack/react-query";
import axios from "axios";

const fetchTodos = async () => {
  const {data} = await axios.get("https://jsonplaceholder.typicode.com/todos");
  return data;
};

export default function Todos() {
  const {data, isLoading, isError} = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodos,
  });

  if (isLoading) return <p>로딩 중...</p>;
  if (isError) return <p>에러 발생!</p>;

  return (
    <ul>
      {data.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

- useQuery를 사용하여 데이터를 가져오며, 자동으로 캐싱됨
- isLoading과 isError를 활용하여 로딩 및 에러 상태를 표시함
- 데이터가 성공적으로 받아지면 화면에 목록을 렌더링

### <b>주요 옵션</b>

| 옵션                     | 설명                           |
|------------------------|------------------------------|
| `queryKey`             | 데이터를 구분하는 키값 (배열 형식 권장)      |
| `queryFn`              | 데이터를 가져오는 비동기 함수             |
| `staleTime`            | 데이터가 "오래됨"을 판단하는 기준 (기본값: 0) |
| `cacheTime`            | 캐시에 저장된 데이터 유지 시간 (기본값: 5분)  |
| `refetchOnWindowFocus` | 창을 다시 포커싱할 때 자동 리패칭 여부       |

## useMutation 사용법 (데이터 변경)

### <b>`useMutation`</b>

`useMutation`은 데이터를 <b>추가, 수정, 삭제</b>할 때 사용하는 기능입니다.

- <b>POST, PUT, DELETE 요청 관리</b>
- <b>성공 / 실패 시 특정 작업 수행 가능</b>
- <b>데이터 변경 후 자동으로 최신 데이터 반영 가능</b>

### 예제 (POST 요청)

```tsx
import {useMutation, useQueryClient} from "@tanstack/react-query";
import axios from "axios";

const addTodo = async (newTodo) => {
  const {data} = await axios.post("https://jsonplaceholder.typicode.com/todos", newTodo);
  return data;
};

export default function AddTodo() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: addTodo,
    onSuccess: () => {
      queryClient.invalidateQueries(["todos"]); // 데이터 최신화
    },
  });

  return (
    <button onClick={() => mutation.mutate({title: "새로운 할 일", completed: false})}>
      추가하기
    </button>
  );
}
```

- 버튼 클릭 시 mutation.mutate() 실행
- addTodo 함수 실행시 API에 새로운 할 일 추가
- 성공 (onSuccess 실행)시 기존 todos 데이터 무효화 (invalidateQueries)
- React Query가 자동으로 todos 데이터를 다시 불러옴
- 새로운 목록이 화면에 업데이트됨

### <b>옵션</b>

| 옵션           | 설명                        |
|--------------|---------------------------|
| `mutationFn` | 데이터를 변경하는 비동기 함수          |
| `onSuccess`  | 성공 시 실행할 콜백 (ex: 데이터 최신화) |
| `onError`    | 에러 발생 시 실행할 콜백            |
| `onSettled`  | 성공/실패 여부와 관계없이 실행         |

## Caching, Stale Time, Invalidation

### <b> 자동 캐싱 (Caching)</b>

- `useQuery`를 사용하면 데이터를 <b>자동으로 캐싱</b>
- 동일한 `queryKey`를 가진 요청은 <b>API 호출 없이 캐시된 데이터 반환</b>

### <b>`staleTime`</b>

(데이터가 "오래됨"을 판단하는 기준)

```tsx
useQuery({
  queryKey: ["todos"],
  queryFn: fetchTodos,
  staleTime: 1000 * 60 * 5, // 5분 동안 새로 요청하지 않음
});
```

### <b> `invalidateQueries`</b>

(데이터 최신화)

```tsx
queryClient.invalidateQueries(["todos"]); // 기존 데이터를 무효화하고 새로 불러오기
```

### <b> 백그라운드 업데이트</b>

(Background Fetching)

```tsx
useQuery({
  queryKey: ["todos"],
  queryFn: fetchTodos,
  refetchOnWindowFocus: true, // 창을 다시 포커스하면 자동 리패칭
  refetchInterval: 10000, // 10초마다 자동 업데이트
});
```

## <b>주의할 점</b>

1. 불필요한 API 호출 방지 <br/>
   staleTime을 설정하여 너무 자주 데이터를 가져오는 것을 방지
   refetchOnWindowFocus: false로 설정하면 창을 다시 열었을 때 불필요한 API 호출을 막을 수 있음
2. 적절한 queryKey 사용 <br/>
   queryKey가 같으면 같은 데이터를 공유하므로, API 엔드포인트마다 고유한 queryKey를 지정해야 함
3. 비동기 함수에서 에러 처리<br/>
   queryFn에서 에러를 처리하지 않으면 useQuery가 자동으로 isError를 true로 설정

## 추가적인 장점

### React Query DevTools

React Query는 공식적으로 DevTools(react-query/devtools)를 제공합니다.
이를 사용하면 현재 쿼리의 상태, 캐싱된 데이터, 요청 상태 등을 시각적으로 확인할 수 있습니다.

```typescript
import {ReactQueryDevtools} from "@tanstack/react-query-devtools";

  <QueryClientProvider client = {queryClient} >
    <App/>
    <ReactQueryDevtools initialIsOpen = {false}/>
  </QueryClientProvider>;
```

### 자동 Garbage Collection

React Query는 사용되지 않는 쿼리를 자동으로 삭제하는 Garbage Collection 기능을 제공합니다.
cacheTime을 설정하면 일정 시간 동안 사용되지 않는 데이터는 메모리에서 해제됩니다.

### Query 병렬 실행

useQueries를 사용하면 여러 개의 데이터를 동시에 가져올 수 있습니다.

```typescript
import {useQueries} from "@tanstack/react-query";
import axios from "axios";

const fetchUsers = async () => axios.get("/api/users");
const fetchPosts = async () => axios.get("/api/posts");

const results = useQueries({
  queries: [
    {queryKey: ["users"], queryFn: fetchUsers},
    {queryKey: ["posts"], queryFn: fetchPosts},
  ],
});
```

## 한눈에 보는 예제

GitHub API에서 TanStack Query 저장소의 정보를 가져오는 코드입니다
```typescript
import {
  QueryClient,
  QueryClientProvider,
  useQuery,
} from '@tanstack/react-query'

const queryClient = new QueryClient()

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}

function Example() {
  const { isPending, error, data } = useQuery({
    queryKey: ['repoData'],
    queryFn: () =>
      fetch('https://api.github.com/repos/TanStack/query').then((res) =>
        res.json(),
      ),
  })

  if (isPending) return 'Loading...'

  if (error) return 'An error has occurred: ' + error.message

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{' '}
      <strong>✨ {data.stargazers_count}</strong>{' '}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  )
}
```

- QueryClientProvider를 사용하여 애플리케이션 전반에서 React Query를 사용할 수 있도록 설정
- useQuery를 사용하여 GitHub API에서 데이터를 가져오고 자동으로 관리
- isPending, error, data를 통해 로딩 상태, 에러 상태, 데이터를 쉽게 관리
- 데이터를 성공적으로 가져오면 UI에 즉시 반영

## 결론 

TanStack Query는 서버 상태 관리의 복잡성을 줄이고, 데이터 패칭 및 캐싱을 쉽게 구현할 수 있도록 도와주는 감사한 라이브러리 입니다.

개발자가 서버 상태 관리에 신경 쓰는 시간을 줄이고, 더 중요한 비즈니스 로직에 집중할 수 있습니다.

- useEffect, useState 없이도 자동으로 서버 데이터 관리 가능
- 캐싱, 동기화, 중복 요청 방지, 백그라운드 업데이트 등의 기능 제공
- 클라이언트 상태(Redux, Context)와 서버 상태(TanStack Query)의 역할을 분리하여 최적화
- API 요청을 최소화하고 성능을 최적화하여, 더 빠르고 부드러운 사용자 경험 제공

React Query(TanStack Query)를 활용하면, 데이터가 애플리케이션을 통제하기 전에 우리가 데이터를 통제할 수 있습니다 - 출처 Tanstack

