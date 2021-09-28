---
title: "[OAuth 2.0]] 구글 로그인 및 유튜브 API 사용하기"
date: "2021-09-28"
tags: ["OAuth2.0"]
---

OAuth 2.0은 웹, 모바일 등의 애플리케이션 API 접근에 대한 사용자 인증을 허가하는 프로토콜이다. 좀 더 쉽게 말하면, 사용자의 권한을 위임받은 한 서비스가 제 3의 서비스에서 사용자와 관련된 API를 활용할 수 있도록 만들어주는 '대리 인증 방식'이라고 할 수 있을 것 같다. 좀 더 쉽게 예시를 들어보자.

철수는 처음 방문한 A사이트에서 'Google 계정으로 로그인 하기'를 클릭하여 손쉽게 로그인을 할 수 있다. 이때 철수는 '사용자'이고, A사이트는 '사용자의 권한을 위임받은 서비스'이며, Google은 '사용자와 관련된 API를 제공하는 제 3의 서비스'인 것이다.

Passport.js 같은 라이브러리를 사용하면 OAuth 2.0을 간편하게 사용할 수 있다. 그러나 라이브러리를 사용했더니인증 과정의 이면에서 도대체 어떤 일이 일어나고 있는지 정확하게 이해할 수가 없었다. 마침 구글의 유튜브 API를 사용하여 예전부터 꼭 만들어보고 싶었던 서비스가 있었는데 드디어 시작하게 되었다. 이를 통해 라이브러리 없이 직접 OAuth 2.0을 이용하여 구글 API를 사용하는 과정을 경험할 수 있었다.

이 글에서는 클라이언트 사이드(서버 X)에서 인증을 거쳐 '사용자의 유튜브 구독 목록' API를 활용하는 웹 애플리케이션인 'ShareTube'를 예시로 들며 OAuth 2.0 인증 절차를 step-by-step으로 정리해두고자 한다. ShareTube는 실제로 이번에 만들기 시작한 프로젝트의 이름이다.

## 들어가기 전에.

앞서 간단히 설명한 것처럼 OAuth 2.0은 3자 간의 소통이 발생한다.

1. 서비스를 사용하려는 유저를 지칭하는 **Resource Owner**
2. 사용자 관련 정보 즉, API를 제공하는 제 3의 애플리케이션을 지칭하는 **Resource Server**
   - 엄밀하게 따지면 Authorization Server와 Resource Server를 구분한다. 뒤에서 설명.
3. 사용자가 이용하고자 하는 본래의 애플리케이션인 **Client**

편의 상 RO, RS, client 라고 지칭한다. RO는 철수, RS는 구글, client는 A사이트 라는 점을 기억하자.

## 0. client 등록 및 자격 얻기

ShareTube라는 웹 애플리케이션이 구글의 API를 이용하기 위해서는 먼저 Google Cloud Platform에 client를 등록하고, 인증 자격(Auth Credential)을 획득해야 한다.

<img src="https://user-images.githubusercontent.com/75300807/135032286-388328fb-f595-4544-b51c-3c786d4811c7.PNG">

프로젝트를 생성한 후에는 ShareTube에서 사용할구글 API 종류를 API 라이브러리에서 활성화 시켜야 한다. ShareTube는 활성화 된 API에 대해서만 요청을 보낼 수 있기 때문이다. [API 라이브러리](https://console.cloud.google.com/apis/library?project=future-abacus-327319)에는 구글이 제공하는 다양한 API들이 있다. ShareTube는 사용자의 유튜브 구독 목록 API를 사용할 것이기 때문에 해당 데이터를 포함하는 YouTube Data API의 사용을 선택한다.

<img src="https://user-images.githubusercontent.com/75300807/135032384-beb30bef-eae7-4b18-9590-5a94030a7b91.PNG">

다음으로 ShareTube는 OAuth 2.0을 위한 Client ID, Client Secret을 발급받아야 한다. ShareTube는 이 Credential을 잘 보관하고 있다가 추후 발생하는 인증 과정에서 구글에게 자신의 자격을 증명하기 위해 사용하게 된다.

해당 프로젝트의 [사용자 인증 정보] 탭에서 [사용자 인증 정보 만들기] - [OAuth 클라이언트 ID]를 선택한다. 이 과정에서 애플리케이션의 유형, 이름, 도메인 등을 입력하는데 현재는 애플리케이션이 배포 되지 않은 상황이니 테스트를 위해 일단 도메인에 `localhost:5000`을 등록해둔다.

그 다음으로는 **Redirect URI**라는 것을 등록해야 한다. Redirect URI는 추후 구글이 ShareTube에게 '인증 토큰'을 전송하게 될 URI이다. `localhost:5000/callback`이라는 임의의 주소를 등록했다.

<img src="https://user-images.githubusercontent.com/75300807/135033997-a3e5abcd-bccb-42c0-aeee-c24f5e293275.PNG">

성공적으로 등록이 되었다면, 다음과 같이Client ID와 Client Secret이 발급된다.

<img src="https://user-images.githubusercontent.com/75300807/135034578-9bd7d9e3-6af7-4b54-85d8-cf5bd48217e0.PNG">

이제 client인 ShareTube의 redirect URL를 포함한 정보가 RS인 구글에 등록되었고, ShareTube는 그 사실을 증명할 ID와 Secret을 발급받았다.

비로소 ShareTube라는 웹 애플리케이션이 구글을 통해 사용자를 인증하고 API를 활용하기 위한 사전 준비가 끝났다. 이제 본격적으로 어떤 순서로 인증 절차가 진행되고 API를 요청하는지에 대해서 알아보자.

## 1. RO를 RS로 안내하기

ShareTube는 구글의 유튜브 API를 통해 사용자인 철수의 유튜브 구독 목록을 받아와야 한다. 철수의 유튜브 구독 목록은 아주 private한 정보이기 때문에 철수의 동의를 구해야만 한다. 구글도 철수의 동의가 없다면 ShareTube에게 철수의 은밀한 데이터를 제공하지 않을 것이다.

ShareTube는 이러한 동의 절차를 철수가 `구글 계정으로 로그인 하기`를 클릭했을 때 수행하도록 만들 예정이다. 이를 위해 ShareTube는 철수가 로그인 버튼을 누르면 **구글 인증 서버**(https://accounts.google.com/o/oauth2/auth)로 안내한다. 이때 URL에 필수적으로 추가하여 함께 인증 서버로 보내야할 내용들이 있다. ShareTube라는 client의 id, redirect uri, response type, scope가 필수 항목에 해당된다.

id와 redirect uri는 앞서 사전 준비 단계에서 client를 등록하고 발급받았던 정보를 동일하게 사용해야 한다. ShareTube 같은 자바스크립트 웹 애플리케이션은 인증 정보가 담긴 'token'을 받아야하기 때문에 response type으로는 token을 설정한다. **scope**는 ShareTube가 '철수의 어떤 정보'를 사용할 것인지에 대한 내용이다. [이 곳](https://developers.google.com/identity/protocols/oauth2/scopes#youtube)에 특정 데이터를 사용하기 위해서는 어떤 scope를 이용해야 할지에 대한 정보가 제공된다. ShareTube는 철수의 유튜브 구독 목록을 이용할 것이기 때문에 해당 목적에 맞는 "https://www.googleapis.com/auth/youtube" 주소를 scope에 담으면 된다.

따라서 철수가 ShareTube에서 구글 로그인 버튼을 클릭했을 때 최종적으로 연결되어야 하는 주소는 다음과 같다.

```
https://accounts.google.com/o/oauth2/auth?client_id=[ShareTube의 client id]&redirect_uri=http://localhost:5000/callback&scope=https://www.googleapis.com/auth/youtube&response_type=token
```

만약 유튜브 구독 목록 외에 다른 데이터를 추가적으로 사용할 예정이라면 여러 개의 scope를 공백으로 구분하여 입력하면 된다.

이제 철수가 로그인 버튼을 클릭하면, ShareTube의 client id, redirect uri 등과 함께 다음과 같은 로그인 페이지로 연결된다.

<img src="https://user-images.githubusercontent.com/75300807/135041867-e9aa2880-f36f-4d18-8168-f486e120cf37.PNG">

철수가 로그인을 완료하면, 구글의 인증 서버는 철수가 들고온 client의 정보 즉, ShareTube의 client id, redirect URI를 검증하여 자격을 보유한 애플리케이션인지 확인한다. 앞서 사전 준비 단계에서 ShareTube를 구글 클라우드 플랫폼에 잘 등록해뒀기 때문에 이 과정을 통과할 수 있을 것이고, 다음으로는 철수에게 자신의 정보를 ShareTube가 사용하는 것을 허락할지에를 묻는 페이지로 이어진다.

<img src="https://user-images.githubusercontent.com/75300807/135043025-391db714-1de0-4fe2-a0fb-76a7f57124da.PNG">

철수가 해당 항목에 체크를 함으로써 구글은 철수라는 사용자가 자신의 정보를 ShareTube가 사용한다는 것에 동의했다는 사실을 기억하게 되고, 이로써 구글은 ShareTube라는 애플리케이션이 철수의 유튜브 구독 목록 정보를 요청하기 위한 자격을 제공한다. 그 자격이 바로 **Access Token**이다.

## Access Token 받기

ShareTube는 구글의 검증을 통과하고, 철수로부터 동의를 얻은 덕분에 Access Token이 발급되었다고 했다. 발급된 Access Token은 어디에 있으며 도대체 어떻게 사용해야 하는 것일까?

다시 앞선 상황으로 돌아가보자. 인증을 끝마친 구글 인증 서버는 철수를 다시 ShareTube로 돌려보내야 할 것이다. 여기에 사용되는 주소가 바로 **Redirect URI**인 것이다. 그리고 구글은 이때 URI의 해시에 access token 정보를 담아서 반환한다. 따라서 철수가 redirect 되는 페이지의 URI는 다음과 같다. (토큰의 값을 aabbcc라고 임의로 가정하자)

```
http://localhost:5000/callback#access_token=aabbcc&token_type=Bearer&expires_in=3600
```

따라서 ShareTube는 `localhost:5000/callback`의 주소에서 `location.hash` 등의 방법을 사용하여 URI에 담긴 access token을 확보할 수 있다.

이제 이 토큰을 사용하여 철수의 유튜브 구독목록을 얻기 위한 구글 API를 호출할 일만 남았다.

## API 호출하기
