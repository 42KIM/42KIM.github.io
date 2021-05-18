---
title: "[Passport.js] 공식 문서 읽기"
date: "2021-05-05"
tags: ["tutorial", "passport", "nodejs"]
---
```Passport```는 '인증(authentication)'이라는 단일 목적을 위해 고안된 Node middleware이다. 모던 웹 애플리케이션에는 다양한 형태의 인증 방식이 존재한다. Passport는 각 애플리케이션의 목적에 맞게, 다양한 ```Strategy```라는 인증 메커니즘을 모듈로 패키지화 해놓았다. 덕분에 우리는 만들고자하는 애플리케이션에 맞는 strategy를 골라 사용할 수 있게 된 것이다. 한마디로, Passport는 웹 애플리케이션을 만드는 과정에서 사용자 인증을 간편하게 구현할 수 있도록 돕는 미들웨어인 것이다.

```javascript
$ npm install passport
```

역시나 npm을 사용해 passport를 설치하는 것으로 시작한다.



### Authenticate

본격적으로 인증은 ```passport.authenticate()```함수를 호출하고, 어떤 strategy를 사용할 것인지 명시하는 것으로 시작한다. 아래 예시 코드는 ```local``` strategy를 사용한다.

```javascript
app.post('/login',
  passport.authenticate('local'),
  function(req, res) {
    // If this function gets called, authentication was successful.
    // `req.user` contains the authenticated user.
    res.redirect('/users/' + req.user.username);
  });
```

디폴트 값으로, 인증에 실패할 경우 Passport는 ```401 Unauthorized``` 상태 코드로 응답하고 다른 라우트 핸들러의 발동이 중지될 것이다. 인증에 성공한 경우, 다음 라우트 핸들러가 발동되며 인증된 사용자에게는 ```req.user``` 프로퍼티가 세팅된다.



### Redirect & Flash Messages

```javascript
app.post('/login',
  passport.authenticate('local', { successRedirect: '/',
                                   failureRedirect: '/login',
                                   failureFlash: true })
);
```

사용자 요청의 인증 후에는 Redirect가 일반적으로 수행된다. 또한 ```failureFlash: true``` 옵션을 추가하여 인증에 실패할 경우, Passport가 왜 인증에 실패했는지에 관한 에러 메시지를 보내도록 설정할 수도 있다. 원한다면 true의 위치에 직접 에러 메시지를 작성하여 담는 것도 가능하다. 

공식 문서에 따르면 **Flash Message 기능을 사용하기 위한 ```req.flash()```함수가 이전에는 내장 함수로 제공되었으나, Express 3.x 버전에서 제거되었다고 한다. 최신 버전에서는 [이 미들웨어](https://github.com/jaredhanson/connect-flash)를 사용하여 대체할 수 있다고 한다.**



### Disable Sessions

인증에 성공하면 Passport는, 사용자의 편의를 위해 브라우저를 종료해도 소멸되지 않는 ```Persistent login session```을 생성한다. 그러나 강화된 보안을 위해 세션을 비활성화 하고 싶다면 ```session``` 옵션을 ```false```로 설정하면 된다.

```javascript
passport.authenticate('basic', { session: false }),
    ...
```



### Configure

Passport를 사용하기 위해서는 세 가지 환경 설정이 필요하다.

1. **Strategies**

Passport는 일반적인 ID/PASSWORD를 식별 방식, OAuth 같은 위임 인증 방식이나 OpenID 같은 다양한 인증 방식을 아우르는 용어로 ```strategies```를 사용한다. 

Passport가 사용자 request를 인증하기 전에 **반드시** 애플리케이션에는 어떤 strategy를 사용할 것인지 설정이 되어 있어야 한다. 설정하는 방법은 아래와 같다.

```javascript
var passport = require('passport')
  , LocalStrategy = require('passport-local').Strategy;

passport.use(new LocalStrategy(
  function(username, password, done) {
    User.findOne({ username: username }, function (err, user) {
      if (err) { return done(err); }
      if (!user) {
        return done(null, false, { message: 'Incorrect username.' });
      }
      if (!user.validPassword(password)) {
        return done(null, false, { message: 'Incorrect password.' });
      }
      return done(null, user);
    });
  }
));
```



```Strategies```를 사용함에 있어서 한 가지 중요한 개념이 있다. 그것은 바로 **Verify Callback**이다. Verify Callback은 유저의 ```credential```을 판별하기 위한 목적으로 사용된다.

Passport는 request를 인증할 때, request 안에 포함된 credential들을 parse한다. 이 과정에서 credential(ID와 비밀번호 등)을 인수로 사용하여 verify callback을 호출한다. 그리고 그 정보가 유효한 것이 인증되면 verify callback 은 ```done()```함수를 호출하여 인증된 ```user```를 Passport에 전달한다.

```javascript
return done(null, user);
```

만약 credential 정보가 유효하지 않는 경우, done()의 인자로 user 대신 ```false```가 전달되어 호출되어야 한다. 이때, 인증 실패의 원인을 설명하는 메시지를 옵션으로 함께 전달할 수 있다.

```javascript
return done(null, false, { message: 'Incorrect password.' });
```

마지막으로 credential 검증 과정에서 예외적인 *서버측 오류* 가 발생하는 경우(데이터베이스 사용 불가 상태 등등), done() 함수는 error를 호출해야 한다.

```javascript
return done(err);
```



2. **Middleware**

Passport를 시작하기 위해서는 ```passport.initialize()``` 미들웨어가 필요하다.