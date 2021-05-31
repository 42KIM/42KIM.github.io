---
title: "[JavaScript] async/await"
date: "2021-05-31"
tags: ["JavaScript", "async", "await"]
---
[이전 글](https://42kim.github.io/TIL/js_promise/)에서 프로미스의 한계를 보완하기 위해 ES8에서 도입된 것이 ```async/await```라고 했다.

async/await를 사용하면 프로미스의 후속 처리 메서드보다 간단하고 가독성 좋게 비동기 처리를 수행할 수 있고, 동기 처리처럼 동작하도록 구현할 수 있다.



### async/await 사용법

+ ```async```는 function 앞에 위치한다.   
  async가 붙은 함수는 **언제나 프로미스를 반환**한다. 프로미스가 아닌 값을 반환하더라도 암묵적으로 resolved 프로미스로 감싸 반환한다.

+ ```await```는 **async 함수 안에서만** 사용할 수 있다.  
  프로미스 앞에 await를 붙이면 자바스크립트는 해당 프로미스가 처리될 때까지 기다린다. 그리고 처리가 완료되면 resolve 결과가 반환된다.

+ 예시

  ```javascript
  async function foo() {
      const a = await new Promise(resolve => setTimeout(() => resolve(1), 3000));
      const b = await new Promise(resolve => setTimeout(() => resolve(2), 2000));
      const c = await new Promise(resolve => setTimeout(() => resolve(3), 1000));
      
      console.log([a, b, c]);
  }
  
  foo();
  ```

  첫 번째 프로미스는 3초가 흐른 후 변수 a에 1을 할당한다.

  이어서 2초가 흐른 후 변수 b에 2를 할당하고,

  다시 1초가 흐른 후 변수 c에 3을 할당하여 약 6초가 소요된다.

  위의 예시는 3개의 비동기 처리를 동기 처리처럼 수행한다.

  만약 서로 관련이 없어서 순차적으로 처리할 필요가 없는 비동기 처리들은 ```Promise.all```을 사용하려 불필요한 소요시간을 줄일 수 있다 [👉Promise.all 설명](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)



### async/await의 에러 핸들링

비동기 함수의 콜백 함수를 호출하는 방식은 try..catch문을 사용해 에러를 캐치할 수 없었다. 그러나 async 함수 내부에서 try..catch문을 사용하면 에러를 정상적으로 캐치할 수 있다.

만약 async 함수 내부에서 try..catch문을 사용하지 않았는데 에러가 발생하면, async 함수는 에러를 reject하는 프로미스를 반환한다. 따라서 이를 처리해줄 프로미스 후속 처리 메서드 ```.catch```를 사용하여 에러 핸들링을 해주면 된다.

```javascript
// async 함수 foo
foo().catch(console.error);
```



### 일반 함수 내부에서 async 함수를 호출하는 경우

일반 함수 내부에서는 await를 사용할 수 없는데, 어떻게 호출된 async 함수의 결과 값을 사용할 수 있을까.

async 함수가 언제나 **프로미스를 반환**한다는 사실을 기억하면 된다. 따라서 프로미스 후속 처리 메서드를 사용하면 결과를 처리할 수 있다.

```javascript
async function wait() {
  await new Promise(resolve => setTimeout(resolve, 1000));

  return 10;
}

function f() {
  // shows 10 after 1 second
  wait().then(result => alert(result));
}

f();
```



#### 참고

[모던 JavaScript 튜토리얼](https://ko.javascript.info/)  

[MDN Web Docs](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)  

모던 자바스크립트 Deep Dive | 위키북스 | 이웅모

