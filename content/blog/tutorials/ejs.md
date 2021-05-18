---
title: "EJS 템플릿 엔진, 초간단 사용법"
date: "2021-05-17"
tags: ["tutorial", "ejs"]
---
### 0. 

프로젝트의 서버가 얼추 갖춰졌다. 동적 페이지 생성을 위해 템플릿 엔진을 사용하기로 했다.

EJS (Embedded JavaScript templating)는 순수 자바스크립트를 사용하여 HTML 마크업을 생성해주는 템플릿 언어이다. 

```javascript
$ npm install ejs
```
먼저 npm을 통해 ejs를 설치하고 express에서 ejs를 require 했다면, ```app.set()```을 사용해

+ ```view engine```으로 사용할 템플릿 엔진 (ejs)

을 세팅해줘야 한다.

```javascript
let ejs = require('ejs');

app.set('view engine', 'ejs');
```

.```render('view', [locals], callback)``` 메서드를 사용하여 브라우저에 렌더링할 HTML 파일을 응답한다. 이때, view.ejs 파일에 전달할 변수를 options 객체에 담아 전달할 수 있다.

```javascript
// index.ejs의 변수 isLogin에 true 값을 전달

app.get('/', (req, res) => {
    res.render('index', {isLogin: true});
});
```



### 1.

HTML에서 자바스크립트를 사용하기 위해 ejs는 고유의 태그를 사용한다. 자바스크립트의 문법적 의미와 무관하게 자바스크립트가 사용되는 라인을 ```<% 'javascript' %>``` 로 감싸준다. Default 구분 문자는 ```%``` 인데 ```{delimiter: ?}``` 객체를 render 메서드의 옵션에 담아 바꿔줄 수도 있다. 혹은 ```ejs.delimiter = '?'``` 를 통해 전역에서 구분 문자를 바꿀 수 있다.  

주요 ejs 태그의 사용법은 아래와 같다.

- `<%` 'Scriptlet' tag, for control-flow, no output

- `<%_` ‘Whitespace Slurping’ Scriptlet tag, strips all whitespace before it

- `<%=` Outputs the value into the template (HTML escaped)

- `<%-` Outputs the unescaped value into the template

- `<%#` Comment tag, no execution, no output

- `<%%` Outputs a literal '<%'

- `%>` Plain ending tag

- `-%>` Trim-mode ('newline slurp') tag, trims following newline

- `_%>` ‘Whitespace Slurping’ ending tag, removes all whitespace after it

  *출처: [EJS Docs](https://ejs.co/#docs)*



### 2.

ejs 파일로 다른 파일의 코드를 불러오기 위해서는 ```include``` 를 사용한다. 중복 HTML escaping을 피하기 위해 ```<%-``` 태그를 사용해준다. include의 첫 번째 인자로는 불러올 파일이 위치한 경로, 두 번째 인자로는 options를 전달한다.

```ejs
<ul>
    <% list.forEach(function(listName){
       <%- include('list/names, {listName: name}); %>
    <% }); %></%>
</ul>
```



### 3.

기존 HTML 태그를 그대로 사용하면서 몇 몇 특수 문자만 추가한다는 점에서는 익숙하게 사용할 수 있다는 장점이 있을 것 같으나, 실수도 꽤 발생하고 가독성이 은근히 떨어지는 것 같기도 하다. 다음 기회에는 또 다른 템플릿 엔진인 ```pug```를 사용해보고 싶다. 이름도 더 귀엽다.