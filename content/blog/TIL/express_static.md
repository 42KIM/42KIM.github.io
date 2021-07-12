---
title: "Express 프레임워크를 도입한 계기"
date: "2021-05-01"
tags: ["nodejs", "Express"]
---
프로젝트를 시작하기에 앞서 "꼭 필요한 순간이 오기 전까지는 무작정 라이브러리를 도입하지 말자"고 뜻을 모았다. 서버 개발은 처음이다보니 지나치게 라이브러리에 의존하기 보다는 각각의 작동원리를 보다 정확하게 알고 넘어가길 희망하는 차원에서 정한 규칙이었다.

Express 프레임워크도 마찬가지다. Node.js에서 거의 필수적으로 그리고 가장 대중적으로 사용되는 만큼, Express의 장점은 수도 없을 테지만 필요한 순간이 오기 전까지는 일단 Node.js의 내장 모듈만을 사용하고 있었다. 

변화의 시점은 생각보다 빨리 찾아왔다. 프로젝트의 view 를 구성하는 HTML, CSS, JS 파일들 즉, ```정적 파일```을 serving 하는 데 있어서 Express의 도움 없이는 그 과정이 너무나 불편했기 때문이다.



#### Express 없이 정적 파일 제공하기

로컬 포트 3000번에 프로젝트의 서버를 열었다고 가정했을 때, 해당 경로로 GET 요청을 보내면 메인 페이지의 HTML 파일이 서빙되어야 한다.

```main.html```이 메인 페이지에 해당되는 html 파일이고 내부에서 \<script> 태그를 사용하여 메인 페이지의 자바스크립트 파일인 ```script.js```를 참조하고 있다고 가정한다면, 서버가 위치한 ```server.js```의 코드는 다음과 같을 것이다. (두 파일 모두 view 디렉토리에 위치한 상태)

```javascript
const http = require('http');
const fs = require('fs');
const app = http.createServer((req, res) =>{
    if(req.url === '/') {
        fs.readFile('./views/main.html', (err, data) => {
            res.writeHead(200, {
                "Content-Type": "text/html"
            });
            res.end(data);
        });
    }
}).listen(3000);
```

지정된 경로로 GET 요청을 보내면, 브라우저는 main.html을 렌더링하여 화면에 출력한다. 동시에 페이지가 무한히 로드되는 오류가 발생한다.

오류의 원인을 찾기 위해 개발자 도구 - Network 탭을 확인해보니, ```script.js``` 파일에 대한 요청이 pending 상태에 멈춰있음을 알 수 있었다.

![사진1](../../../src/images/staticfile.PNG)



즉, 이를 통해서 우리는 브라우저가 ```main.html``` 파일 내부에 \<script> 태그로 참조하고 있는 ```script.js``` 파일을 자동으로 함께 요청하고 있음을 알 수 있었다. 이는 ```main.html``` 파일을 서빙할 때 ```script.js```가 함께 서빙되는 것이 아니라, 해당 파일을 서빙해줄 별개의 처리가 필요하다는 것을 뜻한다. 따라서 메인 페이지의 자바스크립트 파일을 서빙하기 위해서 ```server.js```에는 다음과 같은 처리가 필요하다.

```javascript
const http = require('http');
const fs = require('fs');
const app = http.createServer((req, res) =>{
    if(req.url === '/') {
        fs.readFile('./views/main.html', (err, data) => {
            res.writeHead(200, {
                "Content-Type": "text/html"
            });
            res.end(data);
        });
    }
    // script.js 파일 서빙
    if(req.url === '/script.js') {
        fs.readFile('./views/script.js', (err, data) => {
            res.writeHead(200, {
                "Content-Type": "text/html"
            });
            res.end(data);
        });
    }
}).listen(3000);
```

이제 localhost:3000에 접속하면, 의도한 대로 ```main.html```과 함께 ```script.js```가 서빙되는 것을 확인할 수 있다.

따라서 위와 같은 방식을 사용한 프로젝트의 규모가 커지게 된다면, 매 요청마다 HTML 파일, JS 파일, 그리고 CSS 파일까지 일일이 주소와 연결하여 응답처리를 해줘야하는 불편함이 생긴다.



#### Express의 정적 파일 제공

이 번거롭고 지저분한 과정을 Express에서는 단 한 줄로 쉽게 처리할 수 있다.

Express가 가진 ```express.static``` 미들웨어 함수를 사용하면, 지정한 디렉토리에 위치한 모든 정적 파일을 알아서 대신 검색하여 제공하기 때문이다.

정적 파일이 위치한 디렉토리가 public이라고 한다면, ```server.js```에서 다음과 같이 static 미들웨어 함수를 호출한다.

```javascript
app.use(express.static('public'));
```

 

 

이처럼 웹 페이지의 가장 기본적인 기능인 정적 파일 제공의 편리함과 코드의 복잡도를 낮추기 위해, Express 프레임워크를 도입하게 되었다. 

이렇게 가장 기본적인 기능조차 프레임워크 없이는 꽤 불편하다는 사실을 처음 알게되어 다소 놀랐다.