---
title: "프런트엔드 개발을 위한 테스트 입문: 챕터 5.4 ~ 5.7 (항해99 사전스터디)"
date: 2025-02-24 21:00:00 +1100
categories: [ Programming, Frontend Testing ]
tags: [ Testing, 항해99 ]
author: <author_id>   
---

5일동안 몸살에 목감기까지 와서 진짜 힘드네요;;
이번주 진짜 바쁜데 빨리 안나아서 걱정입니다..
다들 건강 챙기세요..! 

--- 

## 아이템 목록 UI component test

Props 로 취득한 목록을 표시하는 테스트를 알아봅니다

한 번에 여러 DOM 요소를 취득하는 방법과 존재하지 않음을 확인하는 매처로 요소가 화면에 존재하는지 확인하는 방법을 알아봅니다.

### 테스트할 UI 컴포넌트 
예) 기사 목록을 표시하는 컴포넌트 
표시할 기사가 없으면 게재된 기사가 없습니다를 표시한다

```typescript
import { ArticleListItem, ItemProps } from "./ArticleListItem";

type Props = {
  items: ItemProps[];
};

export const ArticleList = ({ items }: Props) => {
  return (
    <div>
      <h2>기사 목록</h2>
      {items.length ? (
        <ul>
          {items.map((item) => (
            <ArticleListItem {...item} key={item.id} />
          ))}
        </ul>
      ) : (
        <p>게재된 기사가 없습니다</p>
      )}
    </div>
  );
};

```
테스트의 중점은 아이템이 존재하는 경우와 아닌경우의 분기 처리입니다.

### 목록에 표시된 내용 테스트

테스트용 데이터를 준비, 데이터는 <ArticleListItem> 에 목록을 표시하기 위한 배열입니다.

```typescript
export const items: ItemProps[] = [
  {
    id: "howto-testing",
    title: "testing with typescript",
    body: "tests written in typescripts makes it more managable"
  }
    ...
]
```

