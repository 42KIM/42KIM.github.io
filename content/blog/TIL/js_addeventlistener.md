---
title: "[JS] addEventListener의 이벤트 핸들러와 this"
date: "2021-06-22"
tags: ["TIL", "JavaScript", "event"]
---
프로젝트에서 ```addEventListener``` 메서드를 사용하면서 겪은 두 가지 문제와 이를 해결한 방법에 관한 글이다.



#### [1] addEventListener의 이벤트 핸들러에 인수 전달하기

프로젝트에서 모달창 여러 개를 만들 일이 생겼다. 각기 다른 버튼을 클릭할 때마다 그에 해당하는 모달을 띄워줘야 했다. A버튼을 클릭하면 A모달이 떠야하고, B버튼을 클릭하면 B모달이 뜨는 식이다. 처음에는 버튼을 클릭했을 때 해당되는 모달을 띄워주는 함수를 각각 만들어 이벤트 핸들러로 등록했다.

```javascript
function open_A_modal() {
    // open A modal
};
function open_B_modal() {
    // open B modal
};

a.addEventListener('click', openAmodal);
b.addEventListener('click', openBmodal);
```

만들고 나서 생각해보니, 모달 마다 함수를 만들어주는 방법은 굉장히 비효율적이었다. 만약 모달이 100개라면 함수도 100개가 필요한 방식이었다..

재사용할 수 있는 함수가 필요했다. 모달을 띄워주는 함수를 하나 만들고, 파라미터에 띄우고자 하는 모달을 인수로 넣어주면 해결될 거라고 생각했다.

```javascript
function open_modal(name) {
    // open 'name' modal
};

a.addEventListener('click', open_modal(A));
b.addEventListener('click', open_modal(B));
```

이렇게 코드를 짜고 나니, 버튼을 클릭도 하지 않았는데 이미 모달들이 떠있는 상황이 발생한다. 왜 그럴까. ```addEventListener``` 메서드의 콜백 함수인 이벤트 핸들러의 특성을 제대로 알지 못했기 때문이다.

addEventListener 메서드를 사용하면 유저가 서비스를 이용하다가 필요 시 버튼을 click 하면 그제서야 이벤트 핸들러인  ```open_modal``` 함수가 브라우저에 의해 호출된다. 따라서 위에 내가 한 것처럼 함수 호출문이 아니라 함수 자체를 등록해둬야 한다. 다시 생각해보면 당연하다. ```open_modal(A)```는 함수를 호출하는 코드니까.

그럼 여기서 의문이 생긴다.

함수에 인수를 전달하려면 함수를 호출해야 하는데, addEventListener에는 함수 호출문을 사용할 수 가 없으니 매개변수를 갖는 함수는 이벤트 핸들러로 사용할 수가 없는 건가??

당연히 그럴 리가 없다. 

해결 방법은 이벤트 핸들러로 **익명 함수**를 등록하여 인수를 전달해야 하는 함수를 감싸는 것이다.

```javascript
function open_modal(name) {
    // open 'name' modal
};

a.addEventListener('click', () => open_modal(A));
b.addEventListener('click', () => open_modal(B));
```

위에서는 익명 함수인 화살표 함수를 이벤트 핸들러로 등록했고, 해당 익명 함수는 내부에서 파라미터를 갖는 ```open_modal``` 함수를 호출하는 함수이다. 이 방법을 통해 버튼이 클릭되면 브라우저는 익명 함수를 호출하고, 호출된 익명 함수가 실행되면서 내부 함수를 호출하는 것이다.



#### [2] 이벤트 핸들러 내부에서의 this

이제 모달을 띄우는 문제는 해결되었다. 그런데 모달을 닫을 때가 문제다. 나는 닫을 때도 마찬가지로 처음에는 각 모달을 일일이 닫는 함수를 만들었다. 모달을 띄울 때 'show' 클래스를 추가하여 ```CSS display```를 ```none```에서 ```flex```로 바꿔주었으니 닫을 때는 해당 클래스를 제거해주면 될 일이었다.

```javascript
function close_A_modal() {
    A_modal.classList.remove('show');
};
function close_B_modal() {
    B_modal.classList.remove('show');
}

close_A_button.addEventListener('click', close_A_modal);
close_B_button.addEventListener('click', close_B_modal);
```

마찬가지로 효율적이지 않은 방법이었다. 사실 모달을 닫는 버튼은 모두 동일한 기능을 수행하기에, 이를 일원화 시키는 것이 필요했다. 그런데 close_button을 클릭할 때마다 현재 떠 있는 모달이 A 모달인지 B 모달인지 알 수가 없으니 인수로 전달할 수도 없는 노릇이다. 따라서 click이 발생하는 버튼이 속해있는 모달을 찾아낼 방법이 필요했다. 

클릭이 발생한 버튼은 현재 띄워져 있는 모달의 자식 노드이기 때문에 이 문제는 ```parentNode``` 프로퍼티나 ```closest``` 메서드를 사용하여 해결 가능하다.

```javascript
function close_modal(e) {
  const cur_modal = e.target.closest('.show');
  cur_modal.classList.remove('show');
};

close_button.addEventListener('click', close_modal);
```

위의 코드는 click 이벤트가 발생하면 생성되는 이벤트 객체의 ```target``` 프로퍼티를 사용해 이벤트가 발생한 DOM 요소(= close_button)를 찾고, 거기서부터 ```closest``` 메서드를 사용하여 현재 'show' 클래스를 가지고 있는 모달을 찾는 방식을 사용했다.

그런데 관련 내용을 알아보다가 ```target``` 프로퍼티 대신 ```this```를 사용할 수도 있다는 것을 알 게 되었다. 

(원래 일반 함수로 호출되는 함수 내부의 ```this```는 전역 객체인 ```window```를 가리킨다. 그리고 메서드 내부에서의 ```this```는 메서드를 호출한 객체를 가리킨다)

그러나 이벤트 핸들러 내부의 this는 일반 함수처럼 window를 가리키는 것이 아니라, 이벤트가 발생한 DOM 요소를 가리킨다고 한다. 따라서 다음과 같이 사용할 수 있다.

```javascript
function close_modal(e) {
  const cur_modal = this.closest('.show');
  cur_modal.classList.remove('show');
};

close_button.addEventListener('click', close_modal);
```

여기서의 this는 click 이벤트가 발생한 DOM 요소인 close_button을 가리킨다. 즉, 이벤트 핸들러 내부의 this는 이벤트 객체의 ```currentTarget``` 프로퍼티와 동일하다.  
(**단!** HTML 요소의 인라인 형식인 **이벤트 핸들러 어트리뷰터 방식**에서의 this는 window를 가리킨다. 이벤트 핸들러 어트리뷰트 값으로 지정한 함수는 일반 함수로서 호출되기 때문이다)

나의 경우처럼, 유사한 기능을 가진 여러 개의 요소들이 같은 이벤트 핸들러를 사용하는 경우 this를 편리하게 사용할 수 있을 것 같다.



간단해 보였지만 addEventListener를 사용할 때 꼭 알고 있어야 할 두 가지 포인트였다.



#### 참고

[MDN Web Docs | Event](https://developer.mozilla.org/ko/docs/Web/API/Event)  
[PoiemaWeb](https://poiemaweb.com/js-event)