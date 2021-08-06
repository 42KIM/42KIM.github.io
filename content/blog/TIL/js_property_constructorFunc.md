---
title: "[TIL] 프로퍼티 어트리뷰트, 생성자 함수"
date: "2021-08-04"
tags: ["TIL", "JavaScript"]
---
본격적으로 자바스크립트의 객체를 파고들기에 앞서,  
프로퍼티 어트리뷰트와 생성자 함수에 대해 확인하고 넘어가고자 한다. 프로퍼티 어트리뷰트는 객체를 구성하는 프로퍼티들에 대해 겉으로는 드러나지 않는 상세한 정보들을 담고 있는데 도대체 뭐가 들었는지를 알아본다.  
또한 그동안은 객체 리터럴을 사용해서만 객체를 만들어왔는데 도대체 생성자 함수는 무엇이길래 또 다른 방법으로 객체를 만들어주는 것인지에 대해서도 알아보려고 한다.



## 프로퍼티 어트리뷰트

자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 **프로퍼티 어트리뷰트**를 기본값으로 자동 정의한다. 프로퍼티의 상태란 '값value', '값의 갱신 가능 여부writable', '열거 가능 여부enumerable', '재정의 가능 여부configurable'를 뜻한다. 이들을 **프로퍼티 플래그**라고도 한다.

각각의 프로퍼티 어트리뷰트에 직접 접근할 수는 없지만, ```Object.getOwnPropertyDescriptor``` 메서드를 사용하면 정보들이 제공되는 **프로퍼티 디스크립터 객체**가 반환되어 간접적으로 확인할 수 있다. 일반적인 방식(객체 리터럴, 프로퍼티 동적 추가 등)으로 프로퍼티를 만들면 프로퍼티 플래그의 값은 모두 ```true```이다. ```Object.defineProperty``` 메서드를 사용하면 프로퍼티의 플래그를 변경할 수 있다. 만약 해당되는 프로퍼티가 없다면 새로운 프로퍼티가 생성되는데 이때 플래그 값을 지정하지 않으면 모든 플래그 값이 ```false```로 생성된다.  

즉, 일반적인 방식으로 생성된 프로퍼티의 플래그 값은 true이지만, defineProperty 메서드로 생성한 프로퍼티의 플래그는 false라는 차이가 있다.

+ ```writable``` 플래그가 true이면, 프로퍼티의 value를 수정할 수 있다. false인 경우 해당 프로퍼티는 읽기 전용 프로퍼티가 된다.

+ ```enumerable``` 플래그가 true이면, for...in 문이나 Object.keys 메서드 등을 사용해 열거할 수 있다.

+ ```configurable``` 플래그가 false이면, 다른 플래그 값의 변경이 불가능하다. 단! ```writable```이 true인 경우 만 false로 바꿀 수 있다. configurable 플래그를 통해 프로퍼티를 영원히 변경할 수 없는 프로퍼티로 만들 수 있게 되는 것이다.

+ ```Object.preventExtensions(obj)```, ```Object.seal(obj)```, ```Object.freeze(obj)``` 등의 메서드를 사용하면 개별 프로퍼티가 아니라 객체 내의 전체 프로퍼티를 대상으로 제약 사항을 수정할 수 있다. (자주 사용할 일은 없다고 함)

  

#### 접근자 프로퍼티 getter와 setter

객체의 프로퍼티는 두 종류로 나뉜다.

1. 데이터 프로퍼티data property : 우리가 알고 있는 그 프로퍼티
2. 접근자 프로퍼티accessor property : 자체적으로는 값을 갖지 않고 **다른 데이터 프로퍼티의 값**을 읽거나 저장할 때 사용하는 접근자 '함수'로 구성된 프로퍼티

접근자 프로퍼티인 getter와 setter 메서드에 대해 알아보자.

```javascript
let player = {
    // 데이터 프로퍼티들
    firstName : 'Lionel',
    lastName : 'Messi',
    // getter 함수
    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    },
	// setter 함수
	set fullName(name) {
        this.firstName = name.split(" ")[0];
        this.lastName = name.split(" ")[1];
    }
}

console.log(player.fullName());	// Lionel Messi
console.log(player.fullname(Heungmin Son));
console.log(player.firstName());	// Heungmin
console.log(player.lastName());		// Son
```

객체 내부에서 ```get```과 ```set```을 사용하여 getter 함수와 setter 함수를 정의할 수 있다. 프로퍼티 접근 연산자를 통해 get 프로퍼티에 접근하면 getter 메서드가 실행되고, set 프로퍼티에 접근하면 setter 메서드가 실행된다. 

사실 getter와 setter 메서드가 없더라도 데이터 프로퍼티의 값을 활용하여 원하는 새로운 값을 구할 수는 있지만, 데이터 프로퍼티를 꺼내오지 않더라도 두 메서드는 일반 프로퍼티 처럼 값을 조작할 수 있도록 해주는 것이다. 함수이지만 함수를 '호출'하지 않고 프로퍼티에 접근하는 것만으로도 원하는 값을 얻을 수 있는 것이다.

접근자 프로퍼티는 데이터 프로퍼티와는 달리 ```value```와 ```writable``` 플래그를 가지고 있지 않다. 그대신 ```get```과 ```set```이라는 함수를 가지고 있다. 데이터 프로퍼티와 마찬가지로 ```defineProperty```에 get과 set을 전달하여 접근자 프로퍼티를 생성할 수 있다.

이제 접근자 프로퍼티가 무엇인지에 대해서는 대략적으로 이해했다. 하지만 실제로 이들이 어떻게 쓰이는지를 짐작하기는 쉽지 않다. ['getter와 setter를 똑똑하게 사용하기', '호환성을 위해 사용하기'](https://ko.javascript.info/property-accessors)를 읽어보면 접근자 프로퍼티의 실제 활용법에 대해서 어느 정도 감을 잡을 수 있을 것 같다.



## 생성자 함수

생성자 함수란 ```new``` 연산자와 함께 호출하여 **객체를 생성**하는 함수를 말한다. 생성자 함수에 의해 생성된 객체를 **인스턴스instance**라고 한다. ```Object``` 생성자 함수 외에도 String, Number, Boolean, Function, Array, Date, RegExp, Promise 등의 생성자 함수가 있다.

```javascript
const string = String("hi");
const objString = new String("hi")

console.log(string);	// hi
console.log(objString);	// String {"hi"}

console.log(typeof(string));	// string
console.log(typeof(objString));	// object
```

이처럼 new 연산자를 생성자 함수와 함께 사용하면 객체, 인스턴스를 반환한다.

그러나 객체 타입의 경우 객체 리터럴로 생성하나 생성자 함수로 생성하나 달라지는 게 없다. 그러면 언제 생성자 함수를 사용하는 걸까.

"프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성하기 위해"라고 할 수 있다. 구조가 완전히 똑같은데 프로퍼티의 값만 달라지는 객체를 100개 만든다고 치자. 객체 리터럴을 사용하면 말 그대로 코드를 100회 타이핑 하며 100개의 객체를 생성해야 한다. 생성자 함수는 **"틀"** 역할을 한다. 미리 틀을 만들어 놓고, 달라지는 내용만 입력하면 동일 구조를 가진 객체를 기계처럼 찍어내는 것이다.

#### 생성자 함수 사용하기

생성자 함수는 일반 함수와 똑같은 방법으로 정의한다. 다만 해당 함수를 생성자 함수로 사용하기 위해서는 두 가지만 기억하면 되겠다.

1. new 연산자를 붙여 호출한다. (그렇지 않으면 일반 함수로 동작한다. 따라서 this는 전역 객체가 된다.)
2. 함수 이름의 첫 글자는 대문자로 시작한다. (생성자 함수임을 알리기 위해)

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}

let person1 = new Person('Kim', 20);
let person2 = new Person('Lee', 30);

console.log(person1);	// Person {name: "Kim", age: 20}
console.log(person2);	// Person {name: "Lee", age: 30}
```

한 가지 더 기억할 것은 **생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다**는 사실이다.  
(일반 함수의 this는 전역 객체, 메서드의 this는 메서드를 호출한 객체를 가리킨다!) 

그 이유는 생성자 함수가 다음의 순서로 실행되기 때문이다.

1. 빈 객체를 만들어 this에 할당(바인딩)한다.
2. 함수의 본문을 실행하며 this에 새로운 프로퍼티를 추가한다.
3. this를 반환한다.

*만약 함수 내부에 return을 통해 다른 객체를 명시적으로 반환하도록 코드를 작성했다면, 새롭게 생성된 this가 아니라 해당 객체가 반환된다. (객체 타입이 아닌 값은 반환하지 않는다. 무시된다.)

#### constructor

그렇다면 모든 함수가 생성자 함수로 호출될 수 있을까? 그렇지는 않다. 

모든 함수는 내부 메서드를 가지고 있는데, [[Construct]]라는 내부 메서드를 가진 함수들만 생성자 함수로서 호출될 수 있다. 이러한 함수 객체를 **constructor**라고 부른다. 그 반대는 non-constructor.

> 모든 함수 객체는 [[Call]] 이라는 내부 메서드를 가지고 있다. (= callable 함수 객체) 
>
> 함수가 '일반 함수'로서 호출 되면 [[Call]]이 호출되고, 생성자 함수로서 호출되면 [[Construct]]가 호출되는 것이다.  모든 함수는 callable 이면서, constructor 이거나 non-constructor 이다.

constructor가 아닌 함수로는 

1. 화살표 함수
2. [메서드 축약 표현](https://poiemaweb.com/es6-enhanced-object-property#3-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%B6%95%EC%95%BD-%ED%91%9C%ED%98%84)으로 선언된 메서드

가 있다. 이 외의 함수들은 new 연산자와 함께 생성자 함수로 호출할 수 있는 것이다. 이는 생성자 함수로 사용할 계획이 없이 만들었던 함수도 new 연산자를 붙여 생성자 함수로 호출할 수 있다는 뜻이다. 다만, 위에서 말했던 것처럼 명시적으로 객체를 반환하지 않거나, ```this```로 프로퍼티 값을 할당해주지 않는 경우에는 빈 객체 {}를 반환하게 된다.





#### 참고

[new 연산자와 생성자 함수](https://ko.javascript.info/prototype-inheritance)  
모던 자바스크립트 Deep Dive | 위키북스 | 이웅모