## 타입스크립트의 event.target

```typescript
const Component = () => {

  const handleClick = (e: React.MouseEvent<HTMLDivElement>) => {
    console.log(e.target.id);	// 에러!
  }

  return (
    <div id="hello" onClick={handleClick}>클릭!</div>
  )
}
```

프로젝트를 앞두고 타입스크립트를 연습해보던 중! 위와 같이 div 요소를 클릭했을 때 해당 요소의 id를 참조하고자 했다. 그런데 e.target에 이어서 .id를 통해 참조하려고 하니 참조되지 않는 오류가 발생했다. e.target은 div 요소인데 어째서 id라는 어트리뷰트가 참조되지 않는 것일까?



### target / currentTarget

해당 내용에 대한 답을 얻기 위해서는 먼저 자바스크립트 이벤트 객체의 target과 currentTarget에 대한 구분이 필요하다.

먼저 currentTarget은 이벤트를 처리하는 요소 즉, 실제로 이벤트 핸들러가 바인딩 된 요소를 가리킨다. 반면 target은 상위 요소로부터 이벤트가 위임되어 실제로 이벤트가 발생하는 위치 즉, 클릭이 발생한 요소를 가리킨다.



이제 다시 타입스크립트로 돌아가보자.

위 코드에서 클릭을 통한 MouseEvent가 발생했을 때, e.target.currentTarget은 id가 'hello'인 div 요소를 가리키는 것이 확실하다.

반면 e.target의 경우, div 요소가 자식으로 다른 요소들을 가지고 있는 경우에 따라서 지칭하는 대상이 달라진다. 즉 실제로 클릭된 요소인 e.target이 ```id```라는 속성을 가지고 있는지 없는지 타입스크립트가 판단을 할 수 없는 상황이다. 마찬가지로 ```e.currentTarget```은 제네릭인 ```HTMLDivElement```라는 사실을 보장할 수 있지만, ```e.target```은 ```HTMLElement```라는 것을 보장할 수 없는 것이다. 따라서 에러가 발생할 수밖에 없다.