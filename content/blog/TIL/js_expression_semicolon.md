---
title: "[TIL] 표현식과 세미콜론 논쟁"
date: "2021-09-04"
tags: ["TIL", "JavaScript", "표현식", "세미콜론"]
---

이번주 자바스크립트 스터디에서 공부하고 토론한 내용 정리.



### 표현식, 문, 토큰

표현식expression은 **값으로 평가될 수 있는 문**이다. 즉 표현식이 평가되면 새로운 값을 생성하거나 기존 값을 참조한다는 것을 의미한다. 식이 됐든, 식별자가 됐든, 함수가 됐든, 값으로 평가되는 모든 문을 가리키는 것이다. 따라서 표현식과 값은 동치라고 할 수 있다. 덕분에 **값이 올 수 있는 모든 자리에는 표현식이 올 수 있게 된다.**

문statement은 프로그램을 구성하는 기본 단위이자 **최소 실행 단위**이다. 프로그램의 기본 단위라는 것은 문의 집합이 프로그램이 된다는 것을 의미하고, 실행 단위라는 것은 문이 컴퓨터에게 무언가를 실행하도록 명령한다는 것을 의미한다. 따라서 문은 명령문이라고도 할 수 있다. 선언문, 할당문, 조건문, 반복문, 함수 선언문 모두 컴퓨터가 무언가를 하도록 만들기 때문에 문인 것이다. 일반적으로 우리가 세미콜론(;)을 사용하여 구분하는 단위가 문이기도 하다.

토큰token은 문법적인 의미를 가지며, **문법적으로 더 이상 나눌 수 없는 가장 작은 단위**를 의미한다. 코드의 기본 요소인 토큰이 모여 문을 구성하는 것이다.

```javascript
const array = [1, 2, 3];
```

위의 코드는 배열을 선언하고 할당하는 '문'이다. 이 문은 변수 선언 키워드인 ```const``` 식별자 ```array``` 할당 연산자 ```=``` 배열 리터럴 ```[]``` 쉼표 ```,``` 숫자 리터럴 ```1``` ```2``` ```3``` 그리고 세미콜론 ```;```이라는 토큰들로 구성되어 있는 것이다. 개인적으로는 토큰을 언어에서의 형태소와 비슷한 개념이라고 이해해도 좋을 것 같다.



### 세미콜론;

세미콜론은 **문의 종료**를 나타내기 위해 사용한다. 단, 중괄호 ```{}``` 로 둘러쌓인 코드 블록은 스스로 문의 종료를 의미하는 자체 종결성을 갖기 때문에 세미콜론을 붙이지 않고 사용한다.

#### 함수선언문 예외 처리

```javascript
// 함수 선언문
function foo1() {
    return;
}

// 함수 표현식
const foo2 = function() {
    return;
};
```

같은 기능을 하는 두 함수가 있다고 하자. 이때 함수 선언문으로 정의한 문의 뒤에는 세미콜론을 붙이지 않는다. 함수 선언문은 코드 블록으로 간주하기 때문에 자체 종결성이 있다고 판단하기 때문이다. 반면 함수 표현식으로 정의된 함수는 말 그대로 표현식이기 때문에 세미콜론을 붙인다.  값으로 평가될 수 있기 때문에 해당 함수는 식별자에 값으로 할당되는 것이다. 

똑같은 리터럴로 정의된 문인데 어째서 다르게 간주되는 것일까? 

이는 자바스크립트가 엔진이 상황에 맞게 예외 처리를 수행하기 때문이다. 엔진은 코드의 문맥을 살펴 함수 선언문을 표현식으로도 해석하여 변수에 할당할 수 있도록 만들어준다. 스터디원 [무호님의 포스팅](https://velog.io/@alang/%EA%B0%92-%EB%A6%AC%ED%84%B0%EB%9F%B4-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%ED%91%9C%ED%98%84%EC%8B%9D)을 참고하자.



#### 세미콜론 자동 삽입 기능, ASI

세미콜론은 문의 종료를 나타낸다고 했는데 이는 필수가 아닌 개발자의 선택사항이다. 그러나 자바스크립트에서는 세미콜론의 존재유무에 따라 개발자의 기대와 다르게 동작하는 경우가 있다. 이는 자바스크립트 엔진이 소스코드를 평가하는 시점에 문의 종료 지점을 예측하여 자동으로 세미콜론을 붙여주기 때문이다. 이를 세미콜론 자동 삽입 기능, ASI (automatic semicolon insertion)라고 한다. 

ASI는 자동으로 세미콜론을 삽입해주니 개발자가 실수로 빠트린 부분을 채워주는 편리함도 있지만, 때로는 개발자의 의도를 벗어나는 결과를 낳기도 한다.

```javascript
// 개발자가 의도한 코드
function foo() {
    return {

    }
}

// 세미콜론을 붙이지 않은 코드
function foo() {
    return
    {
        
    }
}
```

개발자는 빈 객체를 반환하는 코드를 작성하려고 한다. 그런데 아래 함수 선언문에서는 return 문에서 개행을 하여 반환할 객체를 정의했다. 이때 ASI에 의해 return 키워드의 바로 뒤에 자동으로 세미콜론이 추가된다. 따라서 개발자가 의도한 객체를 반환하는 대신, ```return;```이 실행되어 undefined 값을 반환하게 된다. 자바스크립트 엔진은 return 문의 뒤를 문의 종료 지점으로 인식하여 세미콜론 자동 삽입이 이루어지기 때문이다.

반면 문의 종료 지점이지만 자바스크립트 엔진이 세미콜론 자동 삽입을 수행하지 않아서 명시적으로 세미콜론을 붙이지 않으면 오류가 발생하는 경우도 있다.

```javascript
for(
	let i=0
	i<10
	i++) {
    // ...
}
```

개발자는 ```for(let i=0; i<10; i++) { ... }```를 의도했지만, 위의 코드에서는 반복문의 조건부 내부의 문들에 세미콜론을 붙이지 않았다. 이 경우 자바스크립트 엔진은 ASI를 수행하지 않기 때문에 위 코드는 SyntaxError를 발생시킨다.

**(, [, +, =, / 로 시작하는 문의 앞**에서는 특히 세미콜론의 위치에 주의해야 한다. 다음 예시를 살펴보자.

```javascript
let a;
let b = [[1, 1, 1], [2, 2, 2]];
a = b;
[0].forEach(element = > console.log(element));	// 0
```

위 코드는 변수 a를 선언한 뒤, 변수 b에는 2차원 배열을 담고, 다시 a에 b를 할당한다. 그리고 마지막 줄에서 forEach 메서드를 통해 배열 [0]의 요소를 순회하여 출력하는 내용이다. 그러나 같은 코드에서 세미콜론을 붙이지 않는 경우 전혀 다른 결과를 낳는다.

```javascript
let a;
let b = [[1, 1, 1], [2, 2, 2]]
a = b
[0].forEach(element = > console.log(element))	// 1 1 1
```

자바스크립트 엔진은 위의 코드를 해석할 때 마지막 줄을 끌어올려 ```a = b[0].forEach(element = > console.log(element))``` 로 평가한다. 따라서 b 배열의 첫 번째 요소인 배열을 forEach 메서드로 순회하게 되는 결과를 낳게되는 것이다. 

```(```로 시작하는 문의 경우도 비슷한 문제가 발생한다.

```javascript
let a
function b() {
    console.log(b);
}

a = b
(() => console.log('저는 즉시 실행 함수입니다.'))()	
// Uncaught TypeError: b(...) is not a function
```

개발자의 의도는 a 변수에 b함수 객체를 할당하고, 그와는 별개로 즉시 실행 함수를 호출하고자 했을 것이다. 그러나 세미콜론을 붙이지 않았기 때문에 마지막 문이 끌어올려져 ```b(() => console.log('저는 즉시 실행 함수입니다.'))()```로 해석되어 ```b(() => console.log('저는 즉시 실행 함수입니다.'))```라는 (올바른 함수가 아닌) 함수를 호출하기 때문에 오류를 반환하는 것이다.

+, =, / 로 시작하는 문의 앞에 세미콜론을 추가하지 않는 경우도 비슷한 방식으로 의도치 않은 연산을 수행하게 한다. 따라서 이러한 오류를 방지하기 위해 (, [, +, =, / 의 바로 앞에 세미콜론을 붙이는 식의 방어코드 스타일도 존재한다. (개인적으로는 어색하게 느껴진다)

```javascript
;[1, 2, 3]...
;(f(...))()
```



#### 세미콜론에 대한 논쟁

이처럼 자바스크립트에서 세미콜론 사용법은 다소 헷갈리는 부분들이 존재한다. 따라서 개발자들 사이에서도 세미콜론을 필수적으로 붙일지, 아니면 세미콜론을 붙이지 않을지에 대해서 유명한 논쟁이 존재해왔다.

세미콜론을 사용해야 한다는 입장 중의 일부를 소개하면, ASI로 인해 개발자의 의도를 벗어나는 오류가 발생할 수 있고, ASI 규칙을 정확하게 기억하기에는 너무 복잡하다는 점 등이 있다. 또한 명시적인 세미콜론 사용을 통해 실행 단위를 명확하게 구분할 수도 있을 것이다.

반면 세미콜론을 붙이지 말아야한다는 입장의 주요한 논리는 **"ASI와 명시적 세미콜론 삽입 스타일은 연관이 없다"**는 것이다. ASI는 개발자가 세미콜론을 넣든 말든 간에 자체적인 규칙을 따라서 동작하기 때문에, 그 동작 원리를 올바르게 이해하여 필요한 경우에만 사용하면 된다는 의견이라고 생각한다. 어찌됐던 ASI는 자바스크립트에 언제나 존재하는 부분이기 때문에 이를 회피하려는 것이 아니라 올바르게 사용해야 한다는 취지가 아닐까 추측해본다.

결국 자신의 코딩 스타일에 따라 세미콜론을 사용할 수도, 안 할 수도 있지만 이러한 판단을 내리기 위해서는 ASI의 동작원리를 제대로 이해하는 것이 선행되어야 한다는 것만은 틀림 없는 사실이다. 아직 자바스크립트를 높은 수준으로 이해하지 못하고 있는 내게는 의도치 않은 오류를 줄이기 위해 당분간은 세미콜론을 사용하는 것이 적합해 보인다. 그리고 추후 ASI에 대한 자세한 학습이 필요할 것이다.



#### 참고

[함수 표현식](https://ko.javascript.info/function-expressions)  
[자바스크립트, 세미콜론을 써야 하나 말아야 하나](https://bakyeono.net/post/2018-01-19-javascript-use-semicolon-or-not.html)  
[Never Use Semicolons](https://feross.org/never-use-semicolons/)  
모던 자바스크립트 Deep Dive | 위키북스 | 이웅모