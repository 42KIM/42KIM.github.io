---
title: "[JS] 깊은 복사/얕은 복사"
date: "2021-06-08"
tags: ["JavaScript"]
---
#### 객체의 얕은 복사와 깊은 복사

[이전 글](https://42kim.github.io/TIL/js_objecttype/)을 통해 자바스크립트에서 객체를 복사하면 어떤 일이 일어나는지에 대해서 확인했다. 객체가 할당된 변수를 새로운 변수에 할당하면, 객체 그 자체가 아니라 객체의 참조 값이 복사되기 때문에 결국 두 변수는 하나의 동일한 객체를 가리키게 된다는 것이다.

그렇다면 진짜로 객체를 복제해서 같은 값을 가진 '동일하지 않은 새로운 객체'를 만들어내려면 어떻게 해야할까? 이때 알아야할 개념이 ```얕은 복사(shallow copy)```와 ```깊은 복사(deep copy)```일 것 같다.



#### 객체 복사하기

**[1] 반복문을 사용해 직접 복사하기**

먼저 새로운 객체를 생성한 뒤, 기존 객체의 프로퍼티들을 순회하며 원시 값을 일일이 복사해주는 방법이 있다. 원시 값은 객체와 다르게, 새로운 값을 생성하기 때문에 가능한 방법이다.

```javascript
let person = {
    name: "Kim",
    age: 30
};

let copyObj = {};

for (let key in person) {
    copyObj[key] = person[key];
};

console.log(person === copyObj);	// false
```

위 예시에서는 ```for..in```문을 사용하여 객체의 프로퍼티를 새로운 객체에 직접 할당했다. 이때 person 객체의 프로퍼티가 원시 타입의 값이었기 때문에 copyObj의 프로퍼티에 할당될 때 동일한 내용의 새로운 값이 생성되었다. 따라서 perosn 객체와 copyObj 객체를 비교했을 때 false, 즉 서로 다른 메모리 공간에 존재하는 객체임을 확인할 수 있는 것이다.



**[2] Object.assign 사용하기**

```Object.assign``` 메서드를 사용하여 객체를 복사하는 방법도 있다. Object.assgin의 첫 번째 인수로는 복사를 받아올 객체, 두 번째 인수로는 복사의 대상이 되는 객체를 전달하면 된다. 두 번째 인수로 전달되는 객체의 프로퍼티가 첫 번째 인수로 전달되는 객체의 프로퍼티로 복사되는 방식이다.

이때, 두 번째 인수부터 여러 개의 객체를 전달할 수 있다. 만약 객체들의 프로퍼티 중 중복되는 이름을 가진 프로퍼티가 있는 경우, 기존의 값이 덮어씌워 진다.

```javascript
let copyObj = {};

let person = {
    name: "Kim",
    age: 30
};

Object.assgin(copyObj, person);

console.log(copyObj);				// {name: "Kim", age: 30}
console.log(copyObj === person);	// false

// 객체 여러 개 전달하기
let obj1 = {name: "Lee"};
let obj2 = {age: 40};

Object.assgin(copyObj, obj1, obj2);

console.log(copyObj);				// {name: "Lee", age: 40}

```



#### 중첩 객체 복사하기

위에서 예시를 통해 살펴본 복사는 객체의 프로퍼티가 원시 타입 값(문자열, 숫자)인 경우였다. 하지만 객체는 프로퍼티로 또다른 객체를 가질 수 있다. 이런 중첩 객체를 위와 같은 방식으로 프로퍼티를 복사하면, 프로퍼티인 객체 그 자체가 아니라 프로퍼티인 객체의 참조 값이 복사될 것이다. 

객체를 프로퍼티 값으로 갖는 다중으로 중첩된 객체가 있을 때, 가장 첫 번째 단계의 프로퍼티인 객체만 복사하는 것을 **얕은 복사**라고 한다. **깊은 복사**는 더 깊은 단계의 프로퍼티인 객체들까지 모두 복사하는 것을 의미한다.

그렇다면 중첩 객체에서 어떻게 프로퍼티인 객체들까지 완전하게 복사할 수 있을까. 위에서 설명한 복사 방법을 사용하면 깊은 복사가 될까?



**[1] Object.assign은 얕은 복사만 가능하다**

```javascript
let copyObj = {};
let person = {
	name: "Kim",
    address: {
        country: "Korea",
        city: "Seoul"
    }
};

Object.assgin(copyObj, person);
console.log(copyObj === person);					// false
console.log(copyObj.address === person.address);	// true
```

따라서 이 경우, person의 프로퍼티인 address를 수정하면 copyObj의 address가 같이 수정되는 문제가 발생한다. 얕은 복사가 이뤄졌기 때문이다.



**[2] lodash 라이브러리 사용하기**

사실 *[1] 반복문을 사용해 직접 복사하기* 방법을 사용하면 깊은 복사가 가능하다. 그러나 중첩이 깊어지고 객체의 크기가 아주 커진다면 반복문으로 일일이 프로퍼티를 원시 단위에서 복사하는 것은 너무나 복잡한 일이 될 것이다.

자바스크립트 라이브러리인 ```lodash```에는 ```.cloneDeep(obj)```이라는 메서드가 있는데, 이걸 사용하여 깊은 복사를 처리하는 방법이 있다. 자세한 내용은 [👉링크](https://lodash.com/)



#### 결론

자바스크립트에서 객체는 참조에 의한 복사가 이루어진다. 그런데 객체를 복제하는 내장 메서드를 지원하지 않기 때문에 새로운 객체를 복제하여 사용하는 데에 어려움(그리고 귀찮음..)이 있다. 사실 객체를 복제하여 사용할 일은 거의 없다고는하지만, 위의 내용을 잘 기억해두고 (알고리즘 문제를 풀 때처럼..) 헷갈리는 일이 없도록 하는 것이 좋겠다.



#### 참고

[객체 복사, 병합과 Object.assign](https://ko.javascript.info/object-copy#ref-1073)  
[Object.assign()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)  
원시 값과 객체의 비교 | 모던 자바스크립트 Deep Dive | 위키북스