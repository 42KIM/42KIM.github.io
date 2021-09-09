앞서 표현식에 관해 공부하던 중 궁금한 점이 생겼다. 

표현식은 평가되어 값을 생성한다고 했다. 그리고 할당문은 표현식이다. 따라서 할당문도 평가되어 값이 된다. 문득 머릿속에 스치는 것이 하나 있었다. 함수의 매개변수에 기본값을 설정하는 코드를 이따금 사용했는데, 그 형태가 할당문이기 때문이다.



#### 기본값 함수 매개변수

```javascript
function printText(text='default text') {
    return text;
}
```

위 함수는 인수에 적절한 값이 전달되어 호출되면 해당 인수를 반환하지만, 아닌 경우 기본값으로 할당한 'default text'가 반환된다. 잘못된 값을 전달받은 경우를 방어하기 위해 주로 사용하는 방식이다.

이를 **기본값 함수 매개변수 default function parameter**라고 한다. 기본값 함수 매개변수는 '인수 값이 없거나 undefined'가 전달될 경우 매개변수에 미리 설정한 기본값을 할당할 수 있도록 해준다. 이때, undefined를 제외한 null 같은 다른 falsy 값을 전달해도 기본값을 반환하지는 않는다. 오로지 undefined에만 해당되는 것이다.



#### 기본값 함수 매개변수는 표현식일까?

위 함수에서 text='default text' 라는 리터럴은 할당문이다. 따라서 런타임에 함수가 호출되어 함수 선언문을 평가하는 과정에서 해당 부분이 값으로 평가되는 것이 아닌가 생각했다. 그러나 만약 값으로 평가된다면 매개변수의 정의 자체가 값이 되어버리니 오류가 발생할 것이다.

```javascript
function bar(1) {
    return;
}
// Uncaught SyntaxError: Unexpected number

function bar('text') {
    return;
}
// Uncaught SyntaxError: Unexpected string

function bar(undefined) {
    return;
}
// 오류가 발생하지 않는다.
```

함수를 정의할 때 매개변수에 '값'을 넣으면 SyntaxError를 발생시킨다. 그러나 undefined는 예외다. 자바스크립트에서 매개변수의 디폴트 값이 undefined이기 때문일 것이라고 생각한다.

그렇다면 text='default text' 라는 문은 표현식이 아닌걸까. 그리고 기본값 매개변수는 어떤 원리로 동작하는 것일까. 이를 알아보기 위해 스터디원과 함께 간략히 테스트를 했다.



#### 가설

먼저 매개변수에 기본값이 할당되는 과정과 해당 함수의 동작 순서를 테스트하기 위해 다음과 같이 test 함수를 정의했다.

```javascript
function test(param = console.log('기본값!')) {
    return `반환값: ${param}`;
}

test();
```

런타임에 함수가 호출되어 함수 선언문이 평가될 때, 매개변수는 암묵적으로 함수의 내부 스코프에서 선언된다고 했다. 그러면 기본값 함수 매개변수도 다음과 같은 단계를 거쳐 동작하지 않을까?

0. 함수가 호출된다.
1. 함수 선언문이 평가되며 표현식인 param = console.log('기본값!') 도 평가 및 실행된다. 
   + 먼저 param이라는 변수가 함수 내부에 선언된다. 
   + 함수 평가가 끝난 이후 함수 실행 단계에서 console.log('기본값!')이 실행되며 '기본값!'을 출력하고, console.log는 undefined라는 값을 반환한다.
   + 앞서 실행된 console.log('기본값!')의 반환값인 undefined를 param 변수에 할당하는 할당문이 실행된다.
2. 함수 호출 시 인수가 전달되지 않았기 때문에 return문이 실행되면 param에 담긴 undefined가 그대로 반환된다.
3. 따라서 최종적으로 '기본값!'과 '반환값: undefined'가 모두 출력된다.

![캡처1](https://user-images.githubusercontent.com/75300807/132103284-bb83d027-1a70-419a-89f2-0f29b2dda957.PNG)

예상한 결과가 출력되었다! 

이제 인수에 값을 담아서 함수를 호출하는 경우를 예상해보자.

0. 함수가 호출된다.
1. 함수 선언문이 평가되며 표현식인 param = console.log('기본값!') 도 평가 및 실행된다. 
   + 먼저 param이라는 변수가 함수 내부에 선언된다. 
   + 함수 평가가 끝난 이후 함수 실행 단계에서 console.log('기본값!')이 실행되며 '기본값!'을 출력하고, console.log는 undefined라는 값을 반환한다.
   + 앞서 실행된 console.log('기본값!')의 반환값인 undefined를 param 변수에 할당하는 할당문이 실행된다.
2. 인수에 전달한 값이 존재하기 때문에 문자열 타입의 값인 '인수!'가 변수 param에 재할당된다. (기존 값인 undefined를 덮어씌운다)
3. 따라서 최종적으로 '기본값!'과 '반환값: 인수!'가 모두 출력된다. 

![캡처2](https://user-images.githubusercontent.com/75300807/132103360-b19423d4-1c65-448e-9a85-118326644641.PNG)

이번엔 '기본값!'이 출력되지 않았다. 이는 console.log('기본값!') 표현식이 실행되지 않았음을 뜻한다. 이로써 예상했던 동작 순서가 틀렸음을 확인할 수 있었다. 



#### 실제 동작 순서

가설은 틀렸다. 가설에서는 기본값인 표현식이 언제나 실행되어 매개변수에 할당된다고 생각했었는데, 인수가 전달되는 경우에는 기본값에 해당되는 표현식 "실행 자체"가 이뤄지지 않았다. 만약 실행되었다면 두 번째 테스트에서도 '기본값!'이라는 출력이 발생했어야 하기 때문이다. 

하지만 궁금증 해결을 도울 내용을 발견했다. 

기본값 함수 매개변수 방식은 ES6에서 추가된 내용이다. ES6 이전 방식으로는 함수 내부에서 직접적으로 인수로 전달되는 값이 undefined인지 검증하여 그에 따라 알맞은 값을 리턴해줘야 했다는 설명도 있었다.

```javascript
// ECMAScript6
function f (x, y = 7, z = 42) {
    return x + y + z
}
f(1) === 50

// ECMAScript5
function f (x, y, z) {
    if (y === undefined)
        y = 7;
    if (z === undefined)
        z = 42;
    return x + y + z;
};
f(1) === 50;
```

위의 코드를 살펴보면, ES5 방식에서는 매개변수에 기본값을 먼저 할당하는 것이 아니라 조건문을 통해 할당할지 말지를 구분하고 있었다. 이 부분을 통해 기본값 함수 매개변수의 동작 순서를 유추해볼 수 있었다. 따라서 앞선 가설에서 매개변수에 기본값이 할당되는 순서를 다음과 같이 수정해야 할 것이다.

> 0. 함수 선언문이 평가되며 표현식인 param = console.log('기본값!') 도 평가 및 실행된다. 
>    + 먼저 param이라는 변수가 함수 내부에 선언된다. 
>    + 함수 평가가 끝난 이후 함수 실행 단계에서 console.log('기본값!')이 실행되며 '기본값!'을 출력하고, console.log는 undefined라는 값을 반환한다.
>    + 앞서 실행된 console.log('기본값!')의 반환값인 undefined를 param 변수에 할당하는 할당문이 실행된다.
>    
>    ...

👉 위 가설을 다음과 같이 수정한다.

0. 함수가 호출된다.
1. 함수 선언문이 평가되며 매개변수가 함수 내부에 선언된다. 그리고 해당 변수는 undefined로 초기화된다.
2. 평가 단계가 끝나면 (동작 원리가 정확히 밝혀지지 않은) 함수 내부적인 조건문을 통하여 인수값을 확인한다.
   + 만약 인수가 없거나 undfined라면, 기본값 함수 매개변수인 param = console.log('기본값!') 이라는 할당문이 실행되고 이때 '기본값!'이 출력된다.
   + undefined가 아닌 값이 인수로 전달되었다면, param = console.log('기본값!')은 평가되지 않고 param 변수에는 해당 인수가 할당된다.
3. return 문이 실행된다.

---

이제 다시 처음 상황으로 돌아가보자.

```javascript
function printText(text='default text') {
    return text;
}

printText();				// 'default text'
printText(undefined);		// 'default text'
printText('new text');		// 'new text'
```

앞서 살펴본 내용에 따르면 text='default text 라는 문은 '특정한 조건을 만족할 때만 실행' 되는 할당문이라고 생각할 수 있을 것 같다. 여기서의 특정한 조건이란 인수가 비어있거나 undefined를 명시적으로 전달했을 때이다. 그리고 undefined가 아닌 값을 인수로 함수를 호출하면, 매개변수에는 인수인 값이 할당되고 함수 내부의 조건문에 따라 매개변수에 기본값을 할당하는 과정이 동작하지 않는다. 

따라서 text='default text'는 표현식일까? 라는 최초의 질문에 대해서는 '특정한 조건 아래에서만 평가되는 표현식이 아닐까'라는 결론을 자체적으로  내려보았다.

스터디원들과 토론하고 실험하며 유추한 내용은 여기까지다. 사실 ES6에서 기본값 함수 매개변수를 사용했을 때 내부적으로 정확하게 어떤 로직에 따라 조건문이 실행되는지를 알 수 있다면 궁금증에 대한 보다 정확한 해답을 얻을 수 있을 것 같다. 위에서 기술한 내용은 어디까지나 유추일 뿐이기 때문이다.



#### 추가

멘토링 시간에 멘토님께 이런 경우 어떤 식으로 문제를 해결할 수 있을지에 대해 여쭤봤다. 멘토님은 자바스크립트 엔진과 ECMAScript 스펙을 참고하면 원하는 답을 얻는 데 가까워질 수 있을 것이라고 답해주셨다. 아직은 스펙을 읽어도 무슨 말인지 잘 모르겠지만... 앞으로는 해결되지 않는 부분이 있다면 스펙을 읽어보는 습관을 길러보자. 
👉 [V8 Javascript engine](https://v8.dev/) 👉 [ECMAScript](https://github.com/tc39/ecma262) 등등



*아 그리고, 위 질문에 대해서는 ES6로 넘어오면서 자바스크립트 엔진이 parameter에 정의된 표현식을 읽을 수 있게 되었다고 말씀해주셨다. 결국 우리가 궁금해하던 '내부 원리'에 대해 알기 위해서는 스펙을 확인해야..



#### 참고

[MDN Web Docs | 기본값 매개변수](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/Default_parameters)  

[ECMAScript 6 - New Features: Overview & Comparison](http://es6-features.org/#DefaultParameterValues)  