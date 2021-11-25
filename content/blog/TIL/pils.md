# 프로젝트를 진행하며 새롭게 알게 된 사실들

#### react router의 route 대소문자 구분

```react
<Route path="/user/login" component={LoginPage} />
```

위와 같이 정의된 라우트가 있을 때, 기본적으로 react router는 path의 대소문자를 구분하지 않는다. 따라서 ```/User/login```, ```/user/Login```, ```UsEr/LoGiN``` 등 어떤 식으로 접근해도 LoginPage를 응답해준다. 

```caseSensitive``` prop을 사용하면 대소문자를 구분하기 시작한다. (default 값이 true)

```react
<Route caseSensitive path="/User/logiN" component={LoginPage} />
```

이제 ```/User/logiN```이 아닌 경로로 접근하는 경우 LoginPage로 연결되지 않는다.

react router의 기존 버전에서는 이 prop의 이름이 sensitive 였지만, 최근 버전 업그레이드를 통해 caseSensitive로 바뀌었다.