자바스크립트의 클로저(closure) 개념을 제대로 이해하기 위해서는 먼저 자바스크립트의 [스코프](https://42kim.github.io/TIL/js_varletconst/), 실행 컨텍스트, 프로토타입 등에 대한 이해가 선행되면 좋을 것 같다. 각각의 자세한 내용은 따로 다루기로 하고 이 글에서는 클로저에 집중하기로 한다.



### 클로저

MDN은 Closure를 다음과 같이 정의한다.

> A **closure** is the combination of a function bundled together (enclosed) with references to its surrounding state (the **lexical environment**). In other words, a closure gives you access to an outer function’s scope from an inner function. In JavaScript, closures are created every time a function is created, at function creation time.

함수와 렉시컬 환경의 조합이라는 건 무슨 뜻이며, 중첩 함수가 외부함수의 스코프에 접근할 수 있는 건 당연한 말 아닌가? 



클로저
(작성중)
<!-- 미리 알고 있으면 더 좋을 개념

- 렉시컬 스코프
- 실행 컨텍스트
- 가비지 컬렉션



결국 클로저를 사용하면, 렉시컬 환경(상위 스코프의 값들)이 전부 가비지 컬렉션에서 제외되기 때문에 메모리적으로는 손해를 보고 있는 건가? -->

