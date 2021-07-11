---
title: "[Docs] Dotenv, 환경 변수"
date: "2021-06-17"
tags: ["tutorial", "dotenv"]
---
```Dotenv```는 ```.env``` 파일에 설정한 key와 value를 ```process.env```에 할당해주는 모듈이다. 즉 원하는 변수와 값을 환경 변수로 사용할 수 있도록 대신 설정해주는 것이다. 프로젝트에서 서버와 DB를 연결할 때의 옵션(host, port, user, password 등)을 코드에 노출시키지 않기 위해 환경 변수로 관리했다.

> **🚨주의🚨**  
> .env 파일을 버전 관리 시스템에 공유하지 않도록 조심해야 한다. 환경 변수로 등록하더라도 .env 파일을 ```gitignore``` 시키지 않으면 의미가 없다.



Dotenv는 환경 설정에 있어서 ```The Twelv-Factor App```라는 앱 개발 방법론에 기반한다. Twelve-Factor는 ```config```를 코드에서 엄격하게 분리하여 환경 변수에 저장하는 것을 지향하는데, 보다 자세한 내용은 [12factor.net](https://12factor.net/config)에서 확인할 수 있다.



### Dotenv 사용법

먼저 .env 파일에 환경 변수로 사용할 변수와 값을 입력한다.

```
// .env
DB_USER = abcd
DB_PASSWORD = 12345
```

dotenv를 가능한 상단에서 require 한다. 그렇지 않으면 .env 파일에 입력한 내용이 dotenv에 의해 process.env에 로드되기 전에 접근하여 undefined를 반환할 위험이 있다.

```javascript
require('dotenv').config();
```

```config``` 는 .env 파일을 읽어 내용을 parse 한 뒤, process.env에 변수와 값을 할당하고 parse 된 key들이 담긴 객체를 반환한다. (오류가 발생하면 ```error``` 키를 반환한다.) 



#### Options

dotenv를 config 할 때 option을 함께 전달할 수 있다.

```javascript
require('dotenv').config({ path: '/.env 파일 경로'});
```

환경 변수를 담은 .env 파일이 위치한 경로를 지정할 수 있다. 생략 시, 디폴트 경로는 ```process.cwd()``` 즉, 현재 Node.js 프로세스의 루트 디렉토리이다.

Path 외에도 ```Encoding``` 옵션(디폴트 utf8)을 변경하거나,

```javascript
require('dotenv').config({ encoding: 'latin1' })
```

```Debug``` 옵션(디폴트 false)을 변경하여 logging을 활성화 시킬 수 있다.

```javascript
require('dotenv').config({ debug: process.env.DEBUG })
```



#### Parse Rules

파싱 엔진은 .env 파일에 key - value 쌍으로 구성된 String 또는 Buffer를 파싱하여 객체를 반환한다. 

.env 파일에 작성한 환경 변수의 파싱 규칙은 다음과 같다.

+ ```KEY=value```를 ```{KEY: 'value'}```로 반환한다.

+ 공백 라인은 무시한다.

+ ```#```로 시작하는 라인은 주석으로 처리한다.

+ inner quotes는 유지된다.

  ```JSON={"VAR": "value"}
  JSON={"KEY": "value"} 👉👉👉 {JSON: "{\"KEY\": \"value\"}"
  ```

+ quote 되지 않은 값의 양끝 공백은 제거된다.

  ```
  KEY= some value 👉👉👉 {KEY: 'some value'}
  ```

+ value의 quote는 escape 된다. (single, double 모두)

  ```
  KEY='value', KEY="value" 👉👉👉 {KEY: "value"}
  ```

+ quote 된 value의 양끝 공백은 유지된다.

  ```
  KEY=" some value " 👉👉👉 {KEY: ' some value '}
  ```

+ double quote 된 value는 줄바꿈이 가능하다.

  ```
  KEY="new\nline" 👉👉👉 {KEY: 'new
  						line'}
  ```



#### 참고  

[npm dotenv](https://www.npmjs.com/package/dotenv)  