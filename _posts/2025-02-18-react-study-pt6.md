---
title: "리액트 스터디 [Lifecycle] (항해99 사전스터디)"
date: 2025-02-18 21:00:00 +1100
categories: [ Programming, React ]
tags: [ Frontend, 실무, 항해99, React ]
author: <author_id>   
---

수업이 이번주에 시작을 해서 정말 정신없는데, 부모님도 시드니까지 오셔서 더 시간이 없네요 ㅠㅠ

담주부터는 일 업무도 많아지는데 걱정입니다..

--

## 생명주기 (Lifecycle)
React의 Effect는 컴포넌트와는 독립적인 생명주기를 가집니다. 컴포넌트는 마운트, 업데이트, 마운트 해제의 생명주기를 따르지만, Effect는 동기화의 시작과 중지라는 두 가지 작업을
수행합니다. 특히, props나 state가 변경될 때마다 Effect는 이러한 동기화 과정을 반복할 수 있습니다.

### Effect의 동작 원리

Effect는 외부 시스템을 현재의 props와 state에 맞게 동기화하는 역할을 합니다. 예를 들어, 채팅 서버에 연결하는 컴포넌트를 생각해봅시다:

```javascript
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();

    return () => {
      connection.disconnect();
    };
  }, [roomId]);

  // ...
}
```

위에서 Effect는 `roomId`가 변경될 때마다 새로운 채팅방에 연결하고, 이전 연결을 해제합니다. 이러한 동작을 위해 Effect는 의존성 배열을 활용하여, 특정 값(`roomId`)이 변경될
때만 재실행되도록 합니다.

### Effect의 의존성 관리

Effect 내부에서 사용하는 모든 반응형 값(props, state 등)은 의존성 배열에 포함되어야 합니다. 이를 통해 Effect가 최신의 props와 state에 동기화될 수 있습니다. 만약 의존성
배열을 빈 배열(`[]`)로 두면, Effect는 컴포넌트가 마운트될 때 한 번만 실행되고 이후에는 실행되지 않습니다. 그러나 이러한 설정은 의도치 않은 동작을 유발할 수 있으므로, 린터를 통해 의존성 배열이
올바르게 설정되었는지 확인하는 것이 중요합니다.

### Effect와 컴포넌트 생명주기의 차이점

컴포넌트는 마운트, 업데이트, 마운트 해제의 생명주기를 따르지만, Effect는 이러한 컴포넌트 생명주기와 독립적으로 동작합니다. Effect는 특정 의존성의 변경에 따라 동기화를 시작하고, 필요에 따라 동기화를 중지합니다. 따라서, 컴포넌트가 마운트된 상태에서도 여러 번 동기화를 시작하고 중지할 수 있습니다.

### 정리

- Effect의 역할: 외부 시스템을 현재의 props와 state에 동기화.
- 의존성 배열: Effect 내부에서 사용하는 모든 반응형 값을 포함하여, 필요한 시점에만 Effect가 재실행되도록 관리.
- 생명주기의 차이: Effect는 컴포넌트의 생명주기와 독립적으로 동작하며, 의존성의 변경에 따라 동기화의 시작과 중지를 관리.
