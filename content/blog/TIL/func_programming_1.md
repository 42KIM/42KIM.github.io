자바스크립트의 함수형 프로그래밍을 이해하기 위해 앞서 알아야할 것들을 먼저 정리해보려고 한다. 일급 객체, Symbol, 이터러블, 고차 함수 등을 차례로 살펴보자.

### 함수는 일급 객체.

자바스크립트의 함수는 일급 객체다. 이는 함수를 객체와 동일하게, '값'으로 사용할 수 있다는 것을 의미한다.

일급 객체의 조건은 다음과 같다.

1. 무명의 리터럴로 생성할 수 있다.

2. 변수나 자료구조에 저장할 수 있다.

3. 함수의 매개변수에 전달할 수 있다. = 인자로 사용될 수 있다.

4. 함수의 반환값으로 사용할 수 있다.

즉, 값이 사용되는 곳이라면 어디든지 함수도 사용될 수 있다는 뜻이다. (변수 할당, 객체의 프로퍼티, 배열 요소 등) 함수도 객체이지만, 일반 객체에는 없는 프로퍼티를 가지고 있기도 하다. 함수의 이름을 담고 있는 ```name```, 인자를 담고 있는 ```arguments```, 그리고 ```prototype``` 프로퍼티 등등.

```javascript
// arguments 프로퍼티의 값은 함수 내부에서만 참조할 수 있는 객체다(이터러블)
// arguments 객체를 사용하면 인자의 개수가 정해지지 않았을 때도
// 인자를 활용하는 함수를 만들 수 있다.
function args() {
    console.log("arguments: ", arguments.length);
}

console.log(args());		// arguments: 0
console.log(args(1));		// arguments: 1
console.log(args(1, 2));	// arguments: 2
console.log(args(1, 2, 3));	// arguments: 3
```

#### 고차 함수

이러한 일급 객체로서의 함수의 성질 덕분에 고차 함수가 존재한다. 고차 함수는 함수를 인수로 전달받거나, 함수를 반환하는 (클로저를 만들어 반환하는) 함수를 말한다. 즉, 함수를 값으로 다루는 함수이다.

함수가 일급 객체라는 사실은 자바스크립트를 통한 **함수형 프로그래밍**을 가능하게 만든다. 

함수형 프로그래밍 관점에서 매우 중요한 자바스크립트의 이터러블, 제너레이터 등을 살펴보기에 앞서 먼저 알아둘 내용이 있다. 바로 ES6에서 자바스크립트의 7번째 데이터 타입으로 추가된 **Symbol**이다.



### Symbol.

심벌은 **고유한** 원시 타입의 값이다. 다른 값과 절대 중복되지 않는 유일무이한 값이라는 뜻이다. 따라서 **유일한 프로퍼티 key**를 만들기 위해 주로 사용한다.

심벌 값을 생성하기 위해서는 Symbol 함수를 호출한다. 다른 원시 타입 값과 달리 리터럴 표기법을 통해 생성하는 것이 불가능하다. 이때 Symbol 함수를 통해 생성된 심벌 값은 다른 값과 절대 중복되지 않는 유일무이한 값이지만 그 값의 실체(?)를 확인할 수는 없다.

```javascript
const example = Symbol();
console.log(example);	// Symbol()
typeof(example);		// "symbol"
```

심벌은 객체가 아닌 원시 타입 값이기 때문에 ```new```와 함께 사용하여 생성자 함수처럼 호출하면 오류가 발생한다.

Symbol 함수의 인자로 문자열을 전달할 수 있지만, 이 문자열은 디버깅 용도로 생성된 심볼 값을 설명하는 데에만 사용되어진다. 실제 심벌 값이랑은 전혀 다르다.



객체의 프로퍼티로 심벌 값을 사용하려면 대괄호를 사용해야 한다. 접근할 때도 마찬가지다. 프로퍼티로서 사용된 심벌 값은 ```for..in```문이나 ```Object.keys```, ```getOwnPropertyNames``` 메서드 등을 사용하여 키 값을 순회하여 출력할 때 배제된다. 이처럼 '심볼형 프로퍼티 숨기기, hiding symbolic property)'라고 불리는 원칙 덕분에 외부 스크립트나 라이브러리는 심벌 키를 가진 프로퍼티에 접근하지 못한다.

ES6에서 도입된 ```getOwnPropertySymbols``` 메서드를 사용하면 심벌 값 프로퍼티를 찾을 수 있다.

```javascript
const obj = {
    [example]: 'symbolValue'
}

console.log(Object.keys(obj));	// []
console.log(Object.getOwnPropertySymbols(obj)[0]);	// 'symbolValue'
```

자바스크립트가 기본 제공하는 빌트인 심벌 값은 Symbol 함수의 프로퍼티 값으로 할당되어 있다. 이 값들을 **Well-known Symbol**이라고 한다. 이터러블을 살펴보며 다룰 ```Symbol.iterator```가 여기에 해당된다.



### Iterable, 이터러블.

지금부터는 ```iterable```, ```iterator```, ```interable protocol```, ```iterator protocol```, ```iterator result object``` 등을 알아보자.

#### 이터러블

반복 가능한 객체 이터러블은 "이터러블 프로토콜을 준수한 객체"를 가리킨다. 대표적으로는 Array, Set, Map, 문자열 등이 있다. 그렇다면 *이터러블 프로토콜*이란 뭘까.

#### 이터러블 프로토콜

 [Symbol.iterator]를 프로퍼티 키로 사용하여 메서드를 직접 구현한 뒤, 호출(); 하면 '이터레이터 프로토콜'을 준수한 이터레이터를 반환한다. 또는 프로토타입 체인을 통해 상속받은 [Symbol.iterator]를 호출해도 마찬가지다. 이러한 규약을 이터러블 프로토콜이라고 한다. 이러한 이터러블 프토로콜을 준수한 객체를 이터러블이라고 한다.

한마디로 말하자면, **이터레이터 프로토콜을 준수하는 이터레이터를 반환하는 객체**가 **이터러블**인 것이다. 이터러블의 특징으로는

+ ```for...of 문```으로 순회할 수 있다.
+ 스프레드 문법의 대상으로 사용 가능
+ 디스트럭처링 할당의 대상으로 사용 가능

이 있다.

그렇다면 *이터레이터 프로토콜*은 또 뭔가.

#### 이터레이터, 이터레이터 리절트 객체, 이터레이터 프로토콜

이터레이터는 ```next()``` 메서드를 가지고 있다. 이터레이터는 next 메서드를 호출하면 이터러블을 순회하며 요소 값을 담은 ```value``` 프로퍼티와 순회 완료 여부를 나타내는 ```done``` 프로퍼티를 가진 객체를 반환한다. 이 객체를 **이터레이터 리절트 객체**라고 부른다. (이터레이터 리절트 객체는 이터러블의 요소 순회가 끝나면 value에는 undefined, done에는 true를 담아 반환한다)

이처럼 next 메서드를 통해 이터레이터 리절트 객체를 만드는 규약을 **이터레이터 프로토콜**이라고 한다. **이터레이터**는 **이터레이터 프로토콜을 준수하는 객체**인 것이다. 

```javascript
// 배열 arr은 이터러블
const arr = [1, 2, 3];
// iterator는 이터러블 arr이 Symbol.iterator 메서드를 통해 반환한 이터레이터
const iterator = arr[Symbol.iterator]();
// 이터레이터는 next 메서드를 통해 value와 done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환
console.log(iterator.next());	// {value: 1, done: false}
console.log(iterator.next());	// {value: 2, done: false}
console.log(iterator.next());	// {value: 3, done: false}
console.log(iterator.next());	// {value: undefined, done: true}
```

즉,

이터러블 프로토콜을 준수하는 객체인 이터러블에는 대표적으로 문자열, 배열, 맵, 셋이 있는데, 이들은 for of 문으로 순회가 가능하며 스프레드 문법과 디스트럭처링 할당의 대상이 될 수 있다. 이터러블의 메서드 [Symbol.iterator]\()를 호출하면, 이터레이터 프로토콜을 준수하는 이터레이터를 반환한다. 반환된 이터레이터는 next() 메서드를 사용하면 이터러블을 순회하며 value와 done 프로퍼티를 갖는 객체를 차례 차례 반환하는데, 이 객체를 이터레이터 리절트 객체라고 부르는 것이다..😭

결국 우리가 알고 있는 일반적인 반복문 ```for(let i=0; i<arr.length; i++){}``` 없이 ```for of 문```으로 이터러블을 순회할 수 있는 이유는 이터러블에는 [Symbol.iterator] 메서드가 존재하고, 그 메서드가 반환하는 이터레이터에는 next 메서드가 있어, 이터레이터 리절트 객체를 반환하기 때문이다.

반대로 [Symbol.iterator] 메서드가 없으면 이터러블이 아니고, for of 문으로 순회할 수가 없다는 것을 의미한다. for of 문은 내부적으로 이터레이터의 next 메서드를 호출하여 반환된 이터레이터 리절트 객체의 value 값을 변수에 할당하는 방식으로 작동하기 때문이다. (done이 true가 될 때까지)

```javascript
// [Symbol.iterator] 메서드 삭제해보기
// 배열은 Array.prototype의 Symbol.iterator 메서드를 상속 받는다.
const arr = [1, 2, 3];

arr[Symbol.iterator] = null;
for(const el of arr) {
    console.log(el);
}						// Uncaught TypeError: arr is not iterable
```

위에서 설명했듯, [Symbol.iterator]를 프로퍼티 키로 갖는 메서드를 직접 정의해도 이터러블을 생성할 수 있다. 이때  반환되는 이터레이터는 이터레이터 프로토콜을 준수하기 위해 next 메서드를 통해 이터레이터 리절트 객체를 반환해야 한다.

```javascript
const customIterable = {
    // Symbol.iterator 메서드 정의
    [Symbol.iterator]() {
        let i = 1;
        // 이터레이터 반환
        return {
            // 이터레이터의 next 메서드 정의
            next() {
                // 이터레이터 리절트 객체 반환
                return i > 3 ? { done: true } : { value: i++, done: false };
            }
        }
    }
}
```

#### 유사 배열 객체

여기서 잠깐 유사 배열 객체에 대해서 짚고 넘어가자. 

유사 배열 객체는 말 그대로 배열과 유사하지만 배열이 아닌 객체를 말한다. 유사 배열 객체의 조건으로는 프로퍼티 key로서 숫자를 가지고 있고 ```length``` 프로퍼티를 가지고 있어야 한다. 인덱스가 존재하기 때문에 배열처럼 인덱스를 통해 값에 접근할 수 있고, length가 있기 때문에 for 문으로 순회할 수 있다.

**Array.from()**

보통의 유사 배열 객체는 이터러블이 아니다. 따라서 for...of 문으로 순회가 불가능하다. Symbol.iterator 메서드가 없다는 뜻이다. ES6에서 도입된 Array.from 메서드를 사용하면 유사 배열 객체를 배열로 변환할 수 있다.

```javascript
const arrLike = {
    0: 1,
    1: 2,
    2: 3,
    length: 3
}

console.log(Array.isArray(arrLike));		// false
console.log(arrLike[Symbol.iterator]());	// arrLike[Symbol.iterator] is not a function
// 배열로 변환
const arr = Array.from(arrayLike);
console.log(Array.isArray(arr));			// true
console.log(arr[Symbol.iterator]());		// Array Iterator {}
```

**유사 배열 객체 + 이터러블**

대표적인 유사 배열 객체로는 ```document.querySelectorAll```이 반환하는 ```NodeList```, ```document.body.children```이 반환하는 ```HTMLCollection``` 같은 DOM Collection 그리고 함수의 ```arguments``` 객체 등이 있다. 그러나 이 유사 배열 객체들은 유사 배열 객체이면서 동시에 이터러블인 특수한 경우다. 따라서 인덱스로 접근할 수 있으면서도 for...of 문으로 순회가 가능하다는 점을 기억하자.



#### 스프레드 문법

이터러블은 스프레드 문법의 대상이 될 수 있다고 했다. 

스프레드 문법은  ES6에서 도입되었다. 스프레드 문법은 값들이 들어있는 집합을 전개하여 개별적인 값의 목록으로 바꿔준다. 단! 스프레드 문법의 결과는 '값'이 아닌 목록이다. 따라서 변수에 할당하는 등 값처럼 사용할 수가 없다는 것을 의미한다. 

스프레드 문법은 **"쉼표로 구분한 값의 목록"**이 사용되는 맥락에서 사용될 수 있다.

**1. 함수 호출문의 인수**

```Math.max()``` 같은 메서드는 인수로 여러 개의 숫자를 필요로 한다. 이때 인수는 배열의 형태로 전달되어서는 안 된다. 따라서 배열의 요소들을 전개시켜주는 스프레드 문법을 사용하면 효율적이다.

🚩 **Rest 파라미터**

...을 사용하는 스프레드 문법은 Rest 파라미터와 사용 형태가 동일하다. 그러나 서로 반대되는 개념이기 때문에 주의해야 한다.

Rest 파라미터는 함수에 전달된 인수들의 목록을 '배열로' 변환시킨다. 반면 스프레드 문법은 '배열을' 인수들의 목록으로 변환한다.

```javascript
// Rest 파라미터
function restParams(...rest) {
    console.log(rest);
}
console.log(restParams(1,2,3));	// [1, 2, 3]

// 스프레드 문법
const arr = [1, 2, 3];
console.log(...arr);	// 1 2 3
```



**2. 배열 리터럴의 요소**

기존에는 두 개의 배열을 하나의 배열로 합치기 위해 ```concat```과 같은 메서드를 사용해야 했다. 그러나 스프레드 문법을 사용하면 메서드 없이 두 배열을 하나로 합칠 수 있다. ```splice```를 사용하여 배열을 배열의 요소로서 추가하거나, ```slice```를 사용해 원본 배열을 복사할 때도 스프레드 문법이 유용하다.

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
// 합치기
const arr3 = [...arr1, ...arr2];
console.log(arr3);	// [1, 2, 3, 4]
// 추가하기
arr1.splice(2, 0, ...arr2);
console.log(arr1);	// [1, 2, 3, 4]
// 복사하기
const arr4 = [...arr3];
console.log(arr4);	// [1, 2, 3, 4]
```

이밖에 이터러블인 유사 배열 객체를 배열로 변환할 때도 스프레드 문법을 사용하면 편리하다.

 

**3. 객체 리터럴의 프로퍼티**

'스프레드 프로퍼티'는 현재 ECMAScript 표준에 제안된 (stage4) 상태다. 객체 리터럴의 프로퍼티 목록에 대해서도 스프레드 문법을 사용할 수 있다는 내용이다. 원래 스프레드 문법의 대상은 이터러블이어야하지만, 스프레드 프로퍼티는 일반 객체를 대상으로도 사용할 수 있다. ```Object.assgin``` 메서드를 대체할 수 있다.



#### 디스트럭처링 할당

스프레드 프로퍼티와 더불어 디스트럭처링 할당의 대상도 이터러블과 객체 리터럴이다. 디스트럭처링 할당은 배열과 같은 이터러블 또는 객체의 구조를 파괴하여 변수에 개별적으로 할당하는 것을 말한다. 객체에서 필요한 값만 추출할 때 유용하게 사용할 수 있다.

+  배열 디스트럭처링 할당

배열 디스트럭처링 할당의 좌변은 변수, 우변은 이터러블이어야 한다. 배열 인덱스의 순서를 따라 차례대로 할당된다. 변수의 개수와 배열 요소의 개수가 일치하지 않아도 상관없다. 변수의 개수만큼만 할당되며, 변수가 더 많은 경우 할당되지 않은 변수의 값은 undefined이다. 좌변의 변수는 배열 리터럴 형태여야 한다.

```javascript
// 변수의 기본값을 설정할 수도 있다.
const [a, b=1, c] = [1, 2, 3];
console.log(a, c);	// 1 1 3
// 기본값이 설정된 변수에 새로운 값이 할당되면 덮어씌워진다.
const [d, e=0, f] = [4, 5, 6];
console.log(d, e, f);	// 4, 0, 6
```

+ 객체 디스트럭처링 할당

객체 디스트럭처링 할당을 위해서는 객체의 프로퍼티 키를 사용해야 한다. 인덱스를 기준으로 할당하던 배열 디스트럭처링과는 달리, 객체 디스트럭처링 할당은 일치하는 프로퍼티 키가 없으면 변수에 값이 할당되지 않는다. 배열과 마찬가지로 좌변의 변수는 객체 리터럴 형태여야 하며, 우변은 객체로 평가될 수 있는 표현식이어야 한다.

```javascript
// 배열과 마찬가지로 기본값을 설정할 수 있다.
// 객체 프로퍼티 키와 다른 이름의 변수에 할당할 수도 있다.
const user = { name: "Kim", age: 20};
const { name, age: old } = user;
console.log(name, old);	// Kim 20
// 함수의 매개변수에도 사용 가능하다.
function introduce({ name, age }) {
    console.log(`I am ${name}, ${age} years old.`);
}
introduce(user);	// I am Kim, 20 years old.
// 중첩 객체에도 사용할 수 있다.
const person = {
    name: "Kim",
    info: {
        age: 20,
        job: "student"
    }
}
const { name, info: { job }} = person;
console.log(name, job);	// Kim student
```



### 제너레이터

ES6에서 도입된 제너레이터는 코드 블록의 실행을 일시 중지했다가 필요할 때 재개할 수 있다. 한 번에 하나의 값만을 반환하는 일반 함수와는 달리, 제너레이터는 여러 개의 값을 필요에 따라 반환(**yield**)할 수 있다. 

호출하면 코드 블록을 실행하는 일반 함수와는 달리, 제너레이터를 호출하면 제너레이터 객체를 반환한다. 제너레이터 객체는 **이터러블인 이터레이터**이다. 따라서 next() 메서드를 호출하면 제너레이터 함수 내부의 ```yield``` 표현식까지 코드를 실행하고, yield된 값을 이터레이터 리절트 객체의 ```value``` 값으로 반환한다.

```javascript
function *gen() {
    yield 1;
    yield 2;
    yield 3;
}

const generator = gen();
console.log(generator);			// gen {<suspended>}
console.log(generator.next());	// {value: 1, done: false}
console.log(generator.next());	// {value: 2, done: false}
console.log(generator.next());	// {value: 3, done: false}
console.log(generator.next());	// {value: undefined, done: true}
```

