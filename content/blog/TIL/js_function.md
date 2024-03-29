---
title: "[TIL] Function"
date: "2021-05-04"
tags: ["TIL", "JavaScript"]
---
#### 함수의 동작 순서

1) 함수를 호출하면 현재의 실행 흐름을 중단하고 호출된 함수로 실행 흐름을 옮긴다.

2) 매개변수(parameter, 인자)에 인수(argument)가 순서대로 할당된다.

> - 인수는 값으로 표현될 수 있는 표현식이어야 한다.
> - 매개변수는 함수 몸체 내부에서 변수와 동일하게 취급된다. 즉, 함수가 호출될 때마다 함수 몸체 내에서 암묵적으로 매개변수가 생성되고, 일반 변수와 마찬가지로 undefined로 초기화 된 이후에 인수가 순서대로 할당되는 것이다.
> - 매개변수의 스코프는 함수 내부다.
> - 함수는 매개변수와 인수의 개수가 일치하는지 체크하지 않는다. 따라서 인수가 부족하거나 초과하더라도 에러는 발생하지 않는다. 인수가 할당되지 않은 매개변수는 undefined로 평가된다.
> - 사실 (초과된 인수를 포함하여) 모든 인수는 암묵적으로 argument 객체의 프로퍼티로 보관된다.

3) 함수 몸체의 문들이 실행된다.



#### 자바스크립트에서 함수를 정의하는 방법

+ 함수 선언문

```javascript
function add(x, y) {
    return x + y;
}
```

**"함수는 함수 이름으로 호출하는 것이 아니라 함수 객체를 가리키는 식별자로 호출한다"**

함수 리터럴에서 함수의 이름은 함수 몸체 내부에서만 참조할 수 있는 식별자다. 즉, 함수 외부에서는 함수 객체를 가리키는 식별자가 없기 때문에 참조할 수 없다. 그런데  함수 선언문은 변수에 함수를 할당하지 않았는데 호출이 가능하다.

이는 자바스크립트 엔진이 함수 선언문을 해석하여 암묵적으로 함수 이름과 동일한 이름의 식별자를 생성하고, 거기에 함수 객체를 할당하기 때문이다.

함수 선언문은 **표현식이 아닌 문**이다. 그래서 실행 시 완료값인 **undefined**가 출력된다. 만약 표현식인 문이라면 표현식이 평가되어 생성된 함수가 출력되어야 한다

> 표현식이 아닌 문?
>
> 표현식이 아닌 문은 '값'으로 평가될 수 없다. 따라서 변수에 할당할 수 없다. 변수에 할당할 수 없다는 것은 참조할 수도 없다는 의미이다. 표현식이 아닌 문을 실행하면 언제나 '완료값'인 undefined를 출력한다.

표현식이 아닌 문은 변수에 할당할 수 없다고 했다. 그러나 함수 선언문은 변수에 할당된다(되는 것처럼 보인다)

```javascript
let add = function add(x, y) {
    return x + y;
}
```

이는 자바스크립트 엔진이 문맥에 따라 해석을 다르게 하기 때문이다. (값으로 사용되어야하는 경우 즉, 피연산자로 사용되는 기명 함수 리터럴의 경우 객체 리터럴로 해석된다.)



+ 함수 표현식

```javascript
let add = function(x, y) {
    return x + y;
}
```

함수 표현식은 함수 리터럴로 생성한 함수 객체를 변수에 할당하는 정의 방식을 뜻한다. 함수 표현식은 함수 리터럴에서 이름을 생략할 수 있다. 이러한 함수를 익명 함수라고 한다.

표현식이 아닌 문인 함수 선언문과 달리, 함수 표현식은 **표현식인 문**이다.



**함수 호이스팅⁉**

> 함수 선언문이 코드의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징

함수 선언문으로 정의한 함수는 함수 선언문 이전에 호출할 수 있다. 그러나 함수 표현식으로 정의한 함수는 함수 표현식 이전에 호출할 수 없다. 이러한 차이는 두 함수의 **생성 시점**이 다르기 때문에 발생한다.

모든 선언문과 마찬가지로 함수 선언문도 런타임 이전에 자바스크립트 엔진에 의해 먼저 실행된다. 이 시점에, 자바스크립트 엔진은 함수 이름과 동일한 이름의 식별자를 암묵적으로 생성하고, 생성된 함수 객체를 할당한다.

반면 var 키워드를 사용해 함수 표현식으로 함수를 정의하고 함수 표현식 이전에 함수를 참조하면 undefined로 평가된다. 따라서 함수를 호출하면 undefined를 호출하는 것과 마찬가지이므로 타입 에러가 발생한다. 함수 표현식으로 정의된 함수는 함수 호이스팅이 아니라 '변수 호이스팅'이 발생하기 때문이다. var 키워드를 사용한 변수 선언문은 런타임 이전에 실행되어 undefined로 초기화되고, 변수 할당문의 값은 런타임에 평가되어 함수 객체가 되기 때문이다.



#### 함수의 인수

자바스크립트 함수의 인수에 관하여 알아둘 것이 있다.

1. **자바스크립트 함수는 매개변수와 인수의 개수가 일치하는지 확인하지 않는다.**  
   따라서 매개변수의 개수보다 인수의 개수가 적은 경우, 할당되지 않은 매개변수의 값은 undefined이다. 반면 초과된 인수의 경우 무시된다. 단, 무시된 인수가 사라지는 것은 아니다. 모든 인수는 암묵적으로 ```arguments``` 객체에 보관된다. 
2. **동적 타입 언어인 자바스크립트의 함수는 매개변수의 타입을 사전에 지정할 수 없다.**  
   따라서 자바스크립트에서 함수를 정의할 때는 조건문을 사용하여 인수의 타입을 확인하는 등, 오류를 방지하디 위해 적절한 인수가 전달되었는지 주의를 기울여 체크해야 한다. (ES6에서 도입된 **매개변수 기본값** 참고)



#### return 반환문

함수는 return 키워드를 사용해 실행 결과를 함수 외부로 반환할 수 있다. 함수 호출은 표현식이다. 따라서 함수 호출 표현식이은 return 키워드의 반환값으로 평가되는 것이다.

return 반환문의 역할은 두 가지이다.

1. 반환문은 함수의 실행을 중단하고 함수 몸체를 빠져나간다. 반환문 이후의 문은 무시된다.
2. 반환문은 return 키워드 뒤에 오는 표현식을 평가해 반환한다. 지정하지 않으면 undefined를 반환한다. 만약 return과 표현식 사이에 줄바꿈이 있는 경우, 세미콜론이 자동으로 추가되어 undefined가 반환된다.

만약 함수의 반환문이 생략되는 경우, 함수는 undefined를 반환한다. 또한 함수 몸체의 내부가 아닌 곳에서 return 키워드를 사용하면 문법 에러가 발생한다.



#### 매개변수의 참조

매개변수는 함수 몸체 내부에서 변수와 동일하게 취급된다. 따라서 변수와 마찬가지로 값의 타입에 따라 다른 방식의 전달이 발생한다. 규칙은 동일하다. 관련 내용 [👉링크](https://42kim.github.io/TIL/js_objecttype/)

````javascript
function copy(primitive, obj) {
    primitive += 1;
    obj.name = 'LEE';
}

let val1 = 0;
let val2 = { name: 'KIM'};

copy(val1, val2);

console.log(val1);	// 0				-> 원본 값은 변경되지 않았다.
console.log(val2);	// { name: 'Lee'}	-> 변경되었다.
````

따라서 의도치 않게 원본 객체를 훼손하지 않도록 주의하여 사용해야 한다.



#### 참고
[함수](https://ko.javascript.info/function-basics)  
모던 자바스크립트 Deep Dive | 위키북스