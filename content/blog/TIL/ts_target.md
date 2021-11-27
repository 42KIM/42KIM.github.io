---
title: "[TIL] 타입스크립트의 event.target"
date: "2021-11-27"
tags: ["TIL", "TypeScript", "Event"]
---

```typescript
const Component = () => {
  const handleClick = (e: React.MouseEvent<HTMLDivElement>) => {
    console.log(e.target.id) // 에러!
  }

  return (
    <div id="hello" onClick={handleClick}>
      클릭!
    </div>
  )
}
```

프로젝트를 앞두고 타입스크립트를 연습해보던 중, 위와 같이 div 요소를 클릭했을 때 해당 요소의 id를 참조하고자 했다. 그런데 e.target의 id를 참조하지 못하는 오류가 발생했다. 위 코드에서 `e.target`은 분명히 id 속성을 가진 div 요소인데 어째서 id가 참조되지 않는 것일까?

## target / currentTarget

해당 내용에 대한 답을 얻기 위해서는 먼저 자바스크립트 이벤트 객체의 `target`과 `currentTarget`에 대한 구분이 필요하다.

currentTarget은 이벤트를 처리하는 요소 즉, 실제로 이벤트 핸들러가 바인딩 된 요소를 가리킨다. 반면 target은 상위 요소로부터 이벤트가 위임되어 실제로 이벤트가 발생하는 위치 즉, 직접적으로 클릭이 발생한 요소를 가리킨다.

다시 코드로 돌아가보자.

위 코드에서 클릭을 통한 MouseEvent가 발생했을 때, e.currentTarget은 id가 'hello'인 div 요소를 가리키는 것이 확실하다.

반면 e.target의 경우, div 요소가 어떤 자식 요소들을 가지고 있느냐에 따라서 가리키는 대상이 달라진다. 즉 실제로 클릭된 요소인 e.target이 `id`라는 속성을 가지고 있는지 없는지 타입스크립트가 확정적으로 판단을 할 수 없는 상황이다. 마찬가지로 `e.currentTarget`은 제네릭인 `HTMLDivElement` 타입이라는 사실을 보장할 수 있지만, `e.target`은 `HTMLElement`라는 것을 보장할 수 없는 것이다. 그러니 id를 참조하려고 하면 에러가 발생할 수밖에 없다.

따라서 위의 div 요소에 대해서는 currentTarget 속성을 사용하여 id에 접근하도록 만들면 된다.

그러나! target 속성을 사용하면서도 id에 접근하는 방법이 없는 것은 아니다.

## 타입 보장하기

이처럼 타입스크립트에서 타입을 확정지을 수 없는 경우 개발자가 타입을 직접적 또는 간접적으로 확정시켜줄 수 있다.

- 타입 단언

  타입 단언은 명시적으로 타입을 지정해주는 것이다. `as`를 사용한다.

  ```typescript
  const Component = () => {
    const handleClick = (e: React.MouseEvent<HTMLDivElement>) => {
      console.log((e.target as Element).id)
    }

    return (
      <div id="hello" onClick={handleClick}>
        클릭!
      </div>
    )
  }
  ```

  이렇게 e.target을 `Element`타입이라고 단언해줌으로써 id 속성에 접근할 수 있게되는 것이다.

- 타입 가드

  타입 가드는 다양한 방식으로 타입을 추론하도록 돕는다. 타입 단언에 비해서는 간접적으로 대상의 타입을 추론한다.

  ```typescript
  const Component = () => {
    const handleClick = (e: React.MouseEvent<HTMLDivElement>) => {
      if (e.target instanceof HTMLDivElement) {
        console.log(e.target.id)
      }
    }

    return (
      <div id="hello" onClick={handleClick}>
        클릭!
      </div>
    )
  }
  ```

  위 코드에서는 e.target이 HTMLDivElement의 instance인 경우에만 다음 코드를 진행한다. 따라서 해당 조건을 통과한 경우 e.target이 id 속성을 가질 수 있다는 것이 보장되는 것이다.

  instanceof를 외에도 다양한 조건 활용해 대상의 타입을 보장하여 원하는 처리를 수행하도록 만들어줄 수 있다.
