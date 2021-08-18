```this```는 자바스크립트 엔진에 의해 **함수 내부**에 암묵적으로 생성되는 특수한 식별자인 **'자기 참조 변수'**이다. 말 그대로 자기 자신을 참조하기 위한 변수인데, 이는 객체지향 프로그래밍과 관련되어 있다. 객체는 상태를 나타내는 프로퍼티와 행동을 나타내는 메서드의 조합이다. 따라서 메서드가 자신이 속한 객체의 상태를 변경하고자 행동하기 위해서는 객체의 상태 혹은 메서드를 활용할 수 있어야 할 것이다. 이때 필요한 것이 자기 참조 변수 this인 것이다.

자기 자신을 참조하기 위해 메서드 내부에서 자신이 속한 객체의 식별자를 사용하여 참조할 수도 있을 것이다. 그러나 해당 식별자가 다른 값으로 덮어씌워진다면, 엉뚱한 값을 참조하게 된다. 

또한 생성자 함수를 사용하여 인스턴스를 호출하는 경우, 아직 생성되지 않은 인스턴스의 식별자를 알 수 없다는 문제가 발생한다.

```javascript
const user = {
    name: 'Kim',
    // 메서드 축약 표현
    getName() {
        console.log(user.name);
    }
}
console.log(user.getName());	// Kim

// 미래를 예측해야 한다.
function User(name) {
    '미래에 생성될 인스턴스 식별자'.name = name;,
    getName = function() {
        console.log('미래에 생성될 인스턴스 식별자'.name);
    }
}
```

그래서 this가 필요하다.



### 동적 바인딩

자바스크립트의 this가 헷갈리는 이유는, this가 **함수 호출 방식**에 따라 다르게 '동적으로' 결정되기 때문이다. 동일한 함수도 '어떻게 호출되냐'에 따라 상황마다 다른 this를 갖게되는 것이다.

> this의 값은 런타임에 결정된다. 컨텍스트에 따라 값이 달라진다는 뜻이다.
>
> 함수(메서드)를 하나만 만들어 재사용할 수 있다는 것은 장점이지만, 실수를 유발한다는 단점도 있다.



#### 일반 함수

일반 함수를 호출할 때, 함수 몸체 내부의 this는 전역 객체 window를 가리킨다.

```javascript
function foo() {
    console.log(this);
}

console.log(foo());		// Window {window: Window, self: Window, …}
```

this에는 **기본적으로 전역 객체가 바인딩** 되어있기 때문이다. 따라서 다른 객체에 바인딩 되지 않는다면 this는 언제나 window 전역 객체를 가리키게 된다.

this는 자신이 속한 객체 참조를 위한 자기 참조 변수이기 때문에, 전역 객체를 가리키게 사용하는 것은 this의 목적에 부합하지 않는다. 따라서 ```strict mode```가 적용된 일반 함수의 this에는 undefined가 바인딩 된다. 

👉 [엄격 모드?](https://ko.javascript.info/strict-mode)

```javascript
'use strict'
function foo() {
    console.log(this);
}

console.log(foo());		// undefined
```



#### 객체 리터럴의 메서드

다음과 같이 객체 리터럴 내부에 정의된 메서드를 객체가 호출하는 경우, this는 해당 객체를 가리킨다.

```javascript
const user = {
    name: 'Kim',
    getThis() {
        console.log(this);
    }
}

console.log(user.getThis());	// {name: "Kim", getName: ƒ}
```

🚨 단, 객체 리터럴 메서드 내부에 중첩 함수로 정의된 경우일지라도 '어떻게 호출되었냐'에 따라 this가 다르게 바인딩된다.

```javascript
const user = {
    name: 'Kim',
    getThis() {
        console.log(this);
        // 메서드의 중첩 함수
        function innerThis() {
            console.log(this);
        }
        // 중첩 함수 호출
        innerThis();
    }
}
// 메서드 호출
console.log(user.getThis());	// {name: "Kim", getThis: ƒ}
								// Window {window: Window, self: Window, …}
```

```user.getThis()``` 처럼 user 객체에 바인딩 되어 호출된 ```getThis``` 메서드와는 다르게, ```innerThis``` 함수는 getThis 함수 내부에서 일반 함수처럼 ```innerThis();``` 호출되었다. 따라서 innerThis의 this는 전역 객체 window를 가리킨다. 

중요한 것은 **"함수는 함수를 호출한 객체에 바인딩 된다"**는 점이다. 어떤 객체에서 메서드가 정의되었는지는 중요하지 않다. 해당 함수를 어떤 객체가 호출했느냐에 따라 this가 결정된다.

**특정 객체의 메서드인 함수 객체는 객체에 포함된 것이 아니라, 독립적으로 존재하는 별도의 객체를 특정 객체의 프로퍼티가 가리키고 있는 것이기 때문이다.**

```javascript
const user1 = {
    name: 'Kim',
    getThis() {
        console.log(this);
    }
}
console.log(user1.getThis());	// {name: "Kim", getThis: ƒ}

// getThis 메서드를 다른 객체의 메서드로 할당하기
const user2 = {
    name: 'Lee'
}
user2.getThis = user1.getThis;
console.log(user2.getThis());	// {name: "Lee", getThis: ƒ}

// getThis 메서드를 일반 함수로 호출하기
const getThis = user2.getThis;	// Window {window: Window, self: Window, …}
```

위의 getThis 함수는 모두 같은 함수이지만, 각기 다른 this를 갖는다. 특정 객체의 메서드로 호출된 this는 해당 객체를 가리키고, 일반 함수로 호출된 ```getThis```는 전역 객체에 바인딩 되어 호출된 것이기 때문에, ```user1```에서 최초로 정의된 메서드일지라도 this 는 전역 객체를 가리키게 된 것이다. 



#### 콜백 함수

콜백 함수도 마찬가지다. 어떠한 함수라도 '일반 함수로 호출'된다면 this에는 전역 객체 window가 바인딩 된다.

```javascript
const user = {
    name: 'Kim',
    getThis() {
        console.log(this);
        // setTimeout의 콜백 함수
        setTimeout(function() {
            console.log(this);
        }, 1000)
    }
}

console.log(user.getThis());	// {name: "Kim", getThis: ƒ}
								// (1초 뒤) Window {window: Window, self: Window, …}
```

```setTimeout```의 콜백 함수가 1초 후 호출되는 시점에 일반 함수로서 호출되었기 때문에, this는 역시 전역 객체에 바인딩 된 상태인 것이다.



#### 생성자 함수

```new```와 함께 생성자 함수에 의해 생성된 인스턴스의 메서드에서 this는 해당 인스턴스를 가리킨다. 즉, 생성자 함수 내부에서의 this는 생성자 함수가 **미래에 생성할 인스턴스**를 가리키는 것이다.

```javascript
function User(name) {
    this.name = name;
}

User.prototype.getThis = function() {
    console.log(this);
}

const user1 = new User('Kim');
console.log(user1.getThis());	// User {name: "Kim"}
```

🚨 단, new 키워드와 함께 호출되지 않는 경우 ```User```함수의 this는 생성될 인스턴스가 아닌 전역 객체 window를 가리킨다. ```User```가 일반 함수로 호출되기 때문이다. 



### 화살표 함수와 this

앞서 this는 자바스크립트 엔진에 의해 함수 내부에 암묵적으로 할당되는 식별자라고 했다. 그러나 **화살표 함수는 this를 갖지 않는다**. 즉, 화살표 함수 내부에는 this라는 특별한 식별자가 존재하지 않기 때문에 일반적인 함수의 식별자 탐색 방식과 마찬가지로 스코프체인을 따라 **상위 스코프의 this 변수**를 탐색하여 참조한다. 이를 **lexical this**라고 한다. this가 마치 렉시컬 스코프처럼 함수가 정의된 위치에 의해 결정되기 때문이다.

```javascript
const user = {
    name: 'Kim',
    getThis() {
        console.log(this);
        const getArrowThis = () => console.log(this);
        getArrowThis();
    }
}

console.log(user.getThis());	// {name: "Kim", getThis: ƒ}
									// {name: "Kim", getThis: ƒ}
```

```getArrowThis``` 함수는 일반 함수로 호출되었으니 this는 전역 객체 window를 가리켜야 한다. 그러나 출력 결과를 살펴보면 getArrowThis의 this가 user 객체를 가리키는 것을 알 수 있다. 이는 getArrowThis 함수가 화살표 함수로 정의되었기 때문이다. 

화살표 함수는 this 식별자는 갖지 않기 때문에, getArrowThis의 내부에서 선언된 this는 자신의 상위 스코프인 ```getThis``` 함수의 몸체 내부를 탐색한다. getThis 함수는 user 객체의 메서드로 호출되었기 때문에 this가 user 객체를 가리킨다. 따라서 getArrowThis의 this도 user 객체를 가리키게 되는 것이다. 

만약 화살표 함수가 전역에서 정의되는 경우라면, 같은 규칙을 따라 this는 상위 스코프인 전역에서의 this가 가리키는 전역 객체를 참조할 것이다.

```javascript
// 다음과 같은 경우를 헷갈리지 말자.
const user = {
    age: 30,
    getAge: () => console.log(this.age)
}

user.getAge();	// undefined
```

user 객체의 메서드로 호출된 ```getAge``` 함수는 30이 아닌 ```undefined```를 반환한다. 그 이유는 user 객체 내부에서 this는 전역 객체 window를 가리키기 때문이다. 때문에 화살표 함수로 정의된 getAge의 this는 전역 객체의 this를 참조한다. 따라서 전역 객체 window에 정의되어 있지 않은 age 식별자를 참조한 결과로 undefined를 반환하는 것이다.

**프로토타입 객체의 프로퍼티**에 화살표 함수를 할당할 때도, this는 전역 객체를 가리킨다.

```javascript
function User(age) {
    this.age = age;
}

User.prototype.getAge = () => console.log(this.age);
const user = new User(30);

console.log(user.getAge);	// undefined
```




