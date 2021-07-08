---
title: "[Docs 읽기] Helmet 모듈"
date: "2021-06-16"
tags: ["tutorial", "helmet"]
---
### Helmet 이란?

```Helmet```은 다양한 HTTP header 설정을 통해 애플리케이션의 보안 강화를 도와주는 모듈이자, ```Expresss``` 프레임워크에서 호환되는 미들웨어이다.  

#### 사용법

```javascript
app.use(helmet());
```

Helmet은 15개의 하위 미들웨어로 구성되어 있는데, 위 방법으로 사용할 경우 11개의 하위 미들웨어가 디폴트로 포함되어 있다. 

따라서 위의 코드는 아래의 코드와 동일하다.

```javascript
app.use(helmet.contentSecurityPolicy());
app.use(helmet.dnsPrefetchControl());
app.use(helmet.expectCt());
app.use(helmet.frameguard());
app.use(helmet.hidePoweredBy());
app.use(helmet.hsts());
app.use(helmet.ieNoOpen());
app.use(helmet.noSniff());
app.use(helmet.permittedCrossDomainPolicies());
app.use(helmet.referrerPolicy());
app.use(helmet.xssFilter());
```

#### Options

다음과 같이 특정 하위 미들웨어에 대한 옵션을 설정할 수도 있다.

```javascript
app.use(
  helmet({
    // set custom option
    referrerPolicy: { policy: "no-referrer" },
    // disable middleware
    contentSecurityPolicy: false,
  })
);
```



### 하위 미들웨어

#### Defaults

```app.use(helmet());```에 의해 디폴트로 실행되는 미들웨어들이다. 

<span style="color:red">(각각에 대한 설명은 관련 내용 심화 학습 후 보강)</span>

+ ```helmet.contentSecurityPolicy(options)``` 
  XSS 공격을 감지하고 일부 완화시켜주는 Content Security Policy(CSP) 헤더 설정

+ ```helmet.expectCt(options)```

+ ```helmet.referrerPolicy(options)```

+ ```helmet.hsts(options)```

+ ```helmet.noSniff()```

+ ```helmet.dnsPrefetchControl(options)```

+ ```helmet.ieNoOpen()```

+ ```helmet.frameguard(options)```

+ ```helmet.permittedCrossDomainPolicies(options)```

+ ```helmet.hidePoweredBy()```

+ ```helmet.xssFilter()```

  

#### Extras

+ ```helmet.crossOriginEmbedderPolicy()``` 
  허가되지 않은 cross-origin 자원의 로드를 방지하는 Cross-Origin-Embedder-Policy(COEP) 헤더 설정
+ ```helmet.crossOriginOpenerPolicy()```
+ ```helmet.crossOriginResourcePolicy()```
+ ```helmet.originAgentCluster()```



#### 참고

[helmet - npm](https://www.npmjs.com/package/helmet)  

[MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

