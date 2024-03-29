---
title: "[TIL] JavaScript의 데이터 타입"
date: "2021-09-11"
tags: ["TIL", "JavaScript"]
---

이번주 진행된 자바스크립트 스터디에서는 데이터 타입에 대해 공부했다. 기존에 데이터 타입에 관해 한 번 [정리한 글](https://42kim.github.io/TIL/js_objecttype/)이 있기 때문에 잘못 알았거나 새롭게 알게된 내용들을 위주로 간략하게 다시 정리해보았다.



#### 자바스크립트의 7가지 데이터 타입

+ 원시 타입
  + 숫자 타입
  + 문자열 타입
  + 불리언 타입
  + undefined 타입
  + null 타입
  + symbol 타입
+ 객체 타입
  + 객체, 함수, 배열 등



#### 숫자 타입

+ JS는 2진수, 8진수, 16진수를 표현하기 위한 별도의 데이터 타입을 제공하지 않는다. 표기는 다르게 할 수 있지만, 참조하면 모두 10진수를 반환한다.

```javascript
let binary = 0b01000001;
let octal = 0o101;
let hex = 0x41;

65 === binary;	 	// true;
binary === octal;	// true;
octal === hex;		// true;
hex === 65;			// true;
```

+ Infinity, -Infinity, NaN 모두 숫자 타입의 값이다.

  ```javascript
  10 / 0;		// Infinity
  10 / -0;	// -Infinity
  ```

+ 🚨 NaN === NaN 은 false 다.

  ```javascript
  if ('string' + 123 === NaN) {
      console.log('I am NaN');
  } else {
      console.log('I am not NaN');
  }
  
  // I am not NaN
  ```



#### 문자열 타입

+ 문자열 타입은 텍스트 데이터를 나타내기 위해 0개 이상의 16비트 유니코드 문자(UTF-16)의 집합으로 표현한다.
+ 따옴표로 감싸지 않은 텍스트는 키워드나 식별자로 인식된다. 따라서 등록된 키워드나 선언된 식별자가 아니라면 ReferenceError가 발생한다.
+ 템플릿 리터럴 내 ${} 내부에 표현식을 삽입하면, 표현식의 평가 결과는 문자열 타입으로 변환된다.



#### undefined 와 null

+ undefined와 null 타입의 값은 undefined와 null 뿐이다.

+ undefined는 자바스크립트 엔진이 변수를 초기화하는 데 사용하는 값이다. 따라서 개발자가 의도적으로 undefined 값을 변수에 할당하는 것은 취지에 어긋난다.

+ 값이 없다는 것을 명시할 때는 undefined가 아닌 null을 할당한다. "의도적 부재" 즉, undefined와 null 모두 값이 없다는 의미이지만 의도성 측면에서 구분하는 것 같다.

+ 할당된 값에 대한 "참조를 명시적으로 제거"하는 목적으로도 null을 사용한다. 이를 통해 자바스크립트 엔진이 가비지 콜렉션을 수행하도록 한다.

+ 함수(메서드)가 유효한 값을 반환할 수 없는 경우 명시적으로 null을 반환하기도 한다.

  + API에서는 `null`을 종종 관련된 객체가 존재하지 않을 때 그 객체 대신 사용한다
+ ```querySelector```가 조건에 맞는 HTML 요소를 찾지 못하는 경우 null 반환.
+ 🚨 null  또한 하나의 데이터 타입이다. 그런데 ```typeof null``` 은 ```object```를 반환한다. 그 이유는 [이 글](https://2ality.com/2013/10/typeof-null.html)을 참고하자.



#### 데이터 타입

+ 자바스크립트 엔진은 리터럴로 정의된 값을 메모리 공간에 저장하기 위해 '확보해야 할 메모리 공간의 크기'를 데이터 타입을 기반으로 판단한다.

+ 그리고 값을 참조할 때 한 번에 읽어들여야 할 메모리 셀의 개수를 판단할 때도 데이터 타입을 참고한다.

  + 숫자 타입의 크기는 8바이트

  + 문자열 타입은 문자 당 2바이트

  + 나머지는 정확한 정보가 없는듯..?

+ 그리고 메모리에 저장된 2진수 값(비트)을 어떻게 해석할지를 결정하기 위해 데이터 타입을 사용한다.
  + 같은 비트 값이라도 숫자로 해석하냐 문자열로 해석하냐에 따라 값이 달라진다.



#### 동적 타이핑 언어

+ **자바스크립트는 선언이 아닌 할당에 의해 변수의 타입이 결정된다.** 정확히 말하면 변수에 할당된 값에 의해 타입이 동적으로 결정된다.

+ 변수의 타입을 변경하는 것도 가능하다.

+ **값의 validation이 중요한 이유!** 게다가 자바스크립트는 암묵적 타입 변환도 존재하기 때문에 더더욱..



### 원시 타입과 객체 타입

- 원시 값은 변경 불가능한 값(immutable value)이다. 반면 객체 타입의 값은 변경 가능한 값(mutable value)이다.
- 원시 값을 변수에 할당하면 변수(확보된 메모리 공간)에는 **실제 값**이 저장된다. 반면 객체를 변수에 할당하면 변수에는 **참조 값**이 저장된다.
- 원시 값을 갖는 변수를 다른 변수에 할당하면 원본의 원시 값이 복사되어 전달된다. 이를 값에 의한 전달(pass by value)라고한다. 반면 객체를 가리키는 변수를 다른 변수에 할당하면 원본의 참조 값이 복사되어 전달된다.(pass by reference)

#### 원시 값의 불변성

변수는 값 자체가 아니라 값이 저장된 메모리 주소가 할당되는 것이다. 따라서 원시 값이 바뀔 수 없다는 것은 변수에 새로운 값을 할당할 수 없다는 것이 아니라 원시 값이 저장되어 있던 메모리 공간에 들어있던 값을 다른 값으로 변경할 수 없다는 뜻이다. 식별자인 변수의 값을 변경하기 위해서는 '재할당'을 해야한다. "읽기 전용 값"

🚩 ```const```로 선언한 상수는 '재할당'이 불가능한 것이다. 이를 불변성과 동일시하지 말 것.

#### 원시 값의 복사

```javascript
let num = 10;
let num_copy = num;
num === num_copy;	// true;

num = 20;
num === num_copy;	// false;
```

```num === num_copy```가 true이기 때문에 식별자 num와 num_copy가 가리키는 값이 같은 메모리 주소의 값이라고 착각할 수 있다. 그러나 ```num_copy = num``` 라는 할당문이 실행되기 전에 표현식인 식별자 num가 어떤 값으로 평가되는지를 생각해보자. num는 10이라는 숫자값으로 평가되고(=> 평가되어 **새로운 메모리 공간**에 생성된다), 

그 새로운 메모리 주소가 num_copy라는 변수에 할당되는 것이다. 즉 식별자 num가 가리키는 메모리 주소가 복사되어 전달되는 것이 아니라 값이 평가되어 생성된 후, 새로운 메모리 주소가 전달되는 것이다. 이를 **값에 의한 전달**이라고 표현한다. 그러니 num와 num_copy는 서로 다른 메모리 공간에 저장된 별개의 값이다.

원시 값은 변경이 불가능한 값이라고 했다. 따라서 변수 num에 20을 재할당 한다면, 식별자 num는 기존값인 10이 저장된 메모리 주소를 참조하던 것을 끊고, 새로운 메모리 공간에 저장된 20이라는 값의 메모리 주소를 참조하도록 변경된다.

🚨🚨 ...**인줄 알았는데, 자바스크립트 엔진이 내부적으로 정확하게 어떻게 작동하는지에 대해서는 명확하지 않다고 한다. **

```num_copy = num``` 할당문이 실행되는 시점에서는 두 식별자가 같은 메모리 주소를 참조하는 것일 수도 있다. 즉, 할당 시점에서는 같은 메모리 주소를 가리키고 재할당 되는 시점(새로운 값 20이 생성되어 num가 참조하는 메모리 주소를 변경하는 시점)에 이르러서야 다른 메모리 주소를 가리킨다는 것이다. 

파이썬은 그렇게 작동한다. 자바스크립트도 그렇게 작동할지도 모른다... ECMAScript 스펙에 정의되어 있지 않기 때문에 불명확하다.

#### 반면 객체는 변경 가능한 값이다.

원시 값을 할당한 식별자는 원시 값이 저장된 메모리 주소를 참조한다. 그 주소를 따라가면 원시 값이 존재하는 것이다. 그러나 객체는 한 단계가 더 추가되어 있다. 객체를 할당한 식별자는 실제로 객체가 저장된 메모리 주소를 참조하는 주소를 참조한다. 

원시 값이 ```식별자``` -> ```메모리 주소 (= 값 존재)``` 이라면,

객체는 ```식별자``` -> ```메모리 주소1 (= 참조값 존재)``` -> ```메모리 주소2 (= 값 존재)``` 이다. 이때 **참조 값**은 메모리 주소2인 것이다.

객체는 변경 가능하다고 했다. 즉 메모리 주소에 저장된 값 자체를 직접적으로 수정할 수 있다는 것을 의미한다. 따라서 값을 변경하더라도 원시 값처럼 새로운 메모리 공간에 저장되지 않는다.

#### 객체의 복사

```javascript
let obj = {name: 'Kim', age: 20};
let obj_copy = obj;
obj === obj_copy;	// true;

obj.age = 30;
obj === obj_copy;	// true;
```

객체가 할당된 변수를 새로운 변수에 할당하면 '참조 값'이 복사된다(**얕은 복사**). 변수 obj와 obj_copy가 참조하는 메모리 주소는 서로 다르다. 하지만 각각의 메모리 주소에서 참조하고 있는 참조 값 즉, 실제로 객체가 저장된 메모리 주소는 같다. 결국 두 변수가 참조하는 객체의 실체가 동일한 것이다. 따라서 객체의 복사를 **참조에 의한 전달**이라고 한다.

그렇기 때문에 둘 중 하나의 객체를 변경하면 나머지의 값도 동일하게 변경되는 것이다.

---

오늘의 학습 내용을 제대로 이해했다면 아래의 문제들을 풀어보며 개념을 복습하자.

```javascript
let str = 'hello'; a
let str_copy = str; a

str === str_copy;	// (1)

str = 'bye';
str === str_copy;	// (2)
					
// (3) 위의 let str_copy = str 문에서 str_copy에는 '다른' 메모리 주소가 할당된다. O/X
```

```javascript
let obj = {
    name: 'Kim',
    address: {
        country: 'Korea',
        city: {
            do: 'Jeju',
        	si: 'Seogwipo'
        }
    }
}
let obj_copy = obj;

obj === obj_copy;	// (4)
obj.address === obj_copy.address;	// (5)

obj.address.country = 'North Korea';
obj.address.country === obj_copy.address.country;	// (6)

// (7) 위의 let obj_copy = obj 문에서 obj_copy에는 무엇이 할당되는가? "참조값"

let obj_copy_copy = { ...obj };

obj === obj_copy_copy;	// (8)
obj.name === obj_copy_copy.name;	// (9);
obj.address === obj_copy_copy.address;	// (10)
obj.address.city === obj_copy_copy.address.city;	// (11)

obj.name = 'Lee';
obj.name === obj_copy_copy.name;	// (12)
```







