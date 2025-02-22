---
title: "리액트 스터디 [React Query] (항해99 사전스터디)"
date: 2025-02-22 18:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---

> <b>React Query는 데이터를 쉽게 가져오고 관리할 수 있는 라이브러리</b>  
> 자동 캐싱, 데이터 최신화, 백그라운드 업데이트 등을 지원하여 API 요청을 효율적으로 처리할 수 있음 

## <b>React Query</b>

React Query는 <b>비동기 데이터 관리</b>를 쉽게 만들어주는 <b>서버 상태 관리 라이브러리</b>입니다.

<b>기존 방식과 비교</b>

- `useState`와 `useEffect`를 사용하여 데이터를 관리하면 <b>비효율적인 코드</b>가 많이 생김
- 데이터를 요청할 때마다 API를 다시 호출해야 하며, 캐싱이 불가능함
- 로딩 상태, 에러 처리 등을 직접 구현해야 해서 코드가 길어짐

<b>React Query를 사용하면?</b>  
- 자동으로 데이터를 캐싱하여 <b>불필요한 API 요청 방지</b>  
- API 데이터가 변경되면 <b>자동으로 최신 데이터 반영</b>  
- 로딩, 에러 상태를 쉽게 관리 (`isLoading`, `isError`)  
- 백그라운드에서 데이터를 <b>자동으로 새로고침</b> 가능


## React Query가 필요한 이유

### <b>기존 방식과의 차이점</b>

| 비교 항목 | 기존 방식 (`useState` + `useEffect`) | React Query |
|--------------|------------------------|----------------|
| <b>데이터 저장</b> | `useState`, Redux Store 등에서 직접 관리 | 자동 캐싱 및 동기화 |
| <b>API 요청</b> | `useEffect`와 `fetch` 사용 | `useQuery`로 간단하게 처리 |
| <b>데이터 최신화</b> | 변경될 때마다 직접 API 다시 호출 | 자동 리패칭 및 백그라운드 업데이트 |
| <b>로딩 / 에러 처리</b> | `useState`로 직접 상태 관리 필요 | `isLoading`, `isError` 자동 제공 |
| <b>성능 최적화</b> | 캐싱 없음 → 불필요한 API 호출 발생 | 자동 캐싱, `staleTime`, `cacheTime` 활용 가능 |

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

### <b>주요 옵션</b>

| 옵션 | 설명 |
|------|------|
| `queryKey` | 데이터를 구분하는 키값 (배열 형식 권장) |
| `queryFn` | 데이터를 가져오는 비동기 함수 |
| `staleTime` | 데이터가 "오래됨"을 판단하는 기준 (기본값: 0) |
| `cacheTime` | 캐시에 저장된 데이터 유지 시간 (기본값: 5분) |
| `refetchOnWindowFocus` | 창을 다시 포커싱할 때 자동 리패칭 여부 |

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

### <b>옵션</b>

| 옵션 | 설명 |
|------|------|
| `mutationFn` | 데이터를 변경하는 비동기 함수 |
| `onSuccess` | 성공 시 실행할 콜백 (ex: 데이터 최신화) |
| `onError` | 에러 발생 시 실행할 콜백 |
| `onSettled` | 성공/실패 여부와 관계없이 실행 |



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
