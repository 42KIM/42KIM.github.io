---
title: "[OAuth 2.0] 구글 로그인 및 유튜브 API 사용하기"
date: "2021-09-28"
tags: ["OAuth2.0"]
---

OAuth 2.0은 웹, 모바일 등의 애플리케이션 API 접근에 대한 사용자 인증을 허가하는 프로토콜이다. 좀 더 쉽게 말하면, 사용자의 권한을 위임받은 한 서비스가 제 3의 서비스에서 사용자와 관련된 API를 활용할 수 있도록 만들어주는 '대리 인증 방식'이라고 할 수 있을 것 같다. 좀 더 쉽게 예시를 들어보자.

철수는 처음 방문한 A사이트에서 'Google 계정으로 로그인 하기'를 클릭하여 손쉽게 로그인을 할 수 있다. 이때 철수는 '사용자'이고, A사이트는 '사용자의 권한을 위임받은 서비스'이며, Google은 '사용자와 관련된 API를 제공하는 제 3의 서비스'인 것이다.

Passport.js 같은 라이브러리를 사용하면 OAuth 2.0을 간편하게 사용할 수 있다. 그러나 라이브러리를 사용했더니인증 과정의 이면에서 도대체 어떤 일이 일어나고 있는지 정확하게 이해할 수가 없었다. 마침 구글의 유튜브 API를 사용하여 예전부터 꼭 만들어보고 싶었던 서비스가 있었는데 드디어 시작하게 되었다. 이를 통해 라이브러리 없이 직접 OAuth 2.0을 이용하여 구글 API를 사용하는 과정을 경험해볼 수 있었다.

이 글에서는 인증을 거쳐 '사용자의 유튜브 구독 목록'을 반환하는 API를 활용하는 클라이언트 사이드(서버 X) 웹 애플리케이션인 'ShareTube'를 예시로 들며 OAuth 2.0 인증 절차를 step-by-step으로 정리해두고자 한다. ShareTube는 실제로 이번에 만들기 시작한 프로젝트의 이름이다.

## 들어가기 전에.

앞서 간단히 설명한 것처럼 OAuth 2.0은 3자 간의 소통을 필요로 한다. 3자는 다음과 같다.

1. 서비스를 사용하려는 유저를 지칭하는 **Resource Owner**
2. 사용자 관련 정보 즉, API를 제공하는 제 3의 애플리케이션을 지칭하는 **Resource Server**
   - 엄밀하게 따지면 인증을 담당하는 Authorization Server와 데이터를 응답해주는 Resource Server로 구분된다.
3. 사용자가 이용하고자 하는 본래의 애플리케이션인 **Client**

편의 상 **RO**, **RS**, **client** 라고 지칭한다. RO는 철수, RS는 구글, client는 ShareTube 라는 점을 기억하자.

## 0. client 등록 및 자격 얻기

ShareTube라는 웹 애플리케이션이 구글의 API를 이용하기 위해서는 먼저 Google Cloud Platform에 ShareTube 프로젝트를 등록하고, 인증 자격(Auth Credential)을 획득해야 한다.

<img src="https://user-images.githubusercontent.com/75300807/135032286-388328fb-f595-4544-b51c-3c786d4811c7.PNG" width=60%>

프로젝트를 생성한 후에는 ShareTube에서 사용할 구글 API를 API 라이브러리에서 활성화 시켜야 한다. ShareTube는 사용이 활성화 된 API에 대해서만 요청을 보낼 수 있기 때문이다. [API 라이브러리](https://console.cloud.google.com/apis/library?project=future-abacus-327319)에는 구글이 제공하는 다양한 API들이 있다. ShareTube는 사용자의 유튜브 구독 목록 API를 사용할 것이기 때문에 해당 데이터를 포함하는 YouTube Data API의 사용을 선택한다.

<img src="https://user-images.githubusercontent.com/75300807/135032384-beb30bef-eae7-4b18-9590-5a94030a7b91.PNG" width=80%>

다음으로 ShareTube는 구글로부터 Client ID, Client Secret을 발급받아야 한다. ShareTube는 이 Credential을 잘 보관하고 있다가 추후 발생하는 인증 과정에서 구글에게 자신이 구글로부터 사전 검증을 받은 애플리케이션이라는 사실을 증명하기 위해 사용해야 한다.

해당 프로젝트의 [사용자 인증 정보] 탭에서 [사용자 인증 정보 만들기] - [OAuth 클라이언트 ID]를 선택한다. 이 과정에서 애플리케이션의 유형, 이름, 도메인 등을 입력하는데 현재는 애플리케이션이 배포 되지 않은 상황이니 테스트를 위해 일단 도메인에 `localhost:5000`을 등록해둔다.

그 다음으로는 **Redirect URI**라는 것을 등록해야 한다. Redirect URI는 추후 구글이 ShareTube에게 '인증 토큰'을 전송하게 될 주소다. `localhost:5000/callback`이라는 임의의 주소를 등록했다.

<img src="https://user-images.githubusercontent.com/75300807/135033997-a3e5abcd-bccb-42c0-aeee-c24f5e293275.PNG" width=50%>

성공적으로 등록이 되었다면, 다음과 같이 Client ID와 Client Secret이 발급된다.

<img src="https://user-images.githubusercontent.com/75300807/135034578-9bd7d9e3-6af7-4b54-85d8-cf5bd48217e0.PNG" width=55%>

이제 redirect URL를 포함하여 ShareTube라는 client의 정보가 RS인 구글에 등록되었고, ShareTube는 그 사실을 증명할 ID와 Secret을 발급받았다.

비로소 ShareTube라는 웹 애플리케이션이 OAuth 2.0을 통해 구글로부터 사용자를 인증하고 API를 활용하기 위한 사전 준비가 끝났다. 이제 본격적으로 어떤 순서로 인증 절차가 진행되고 API를 요청하는지에 대해서 알아보자.

## 1. RO의 권한을 위임 받기

ShareTube는 구글의 유튜브 API를 통해 사용자인 철수의 유튜브 구독 목록을 받아와야 한다. 철수의 유튜브 구독 목록은 사적인 정보이기 때문에 철수의 동의를 구해야만 한다. 구글의 입장에서도 철수의 동의가 없다면 ShareTube라는 애플리케이션에 철수의 데이터를 제공하지 않을 것이다.

ShareTube는 철수로부터 동의를 구하기 위해 철수가 `구글 계정으로 로그인 하기`를 클릭했을 때 동의 절차를 수행하도록 만들 예정이다. 이를 위해 ShareTube는 철수가 로그인 버튼을 누르면 **구글 인증 서버**(`https://accounts.google.com/o/oauth2/auth`)로 안내한다. 이때 쿼리 매개변수에 추가하여 함께 인증 서버로 보내야할 내용들이 있다. ShareTube의 client id, redirect URI, response type, scope가 그 필수 항목에 해당된다.

id와 redirect uri는 앞서 사전 준비 단계에서 사용한 정보를 동일하게 사용해야 한다.

ShareTube 같은 자바스크립트 웹 애플리케이션은 인증 정보가 담긴 'token'을 받아야하기 때문에 response type으로는 token을 설정한다.

**scope**는 ShareTube가 '철수의 어떤 정보'를 사용할 것인지에 대한 내용이다. [이 곳](https://developers.google.com/identity/protocols/oauth2/scopes#youtube)에 특정 데이터를 사용하기 위해서는 어떤 scope를 이용해야 할지에 대한 정보가 제공된다. ShareTube는 철수의 유튜브 구독 목록을 이용할 것이기 때문에 해당 목적에 맞는 scope인 `https://www.googleapis.com/auth/youtube`를 쿼리 매개변수에 담으면 된다.

따라서 철수가 ShareTube에서 구글 로그인 버튼을 클릭했을 때 최종적으로 연결되어야 하는 주소는 다음과 같다. `구글 인증 서버 + client_id + redirect_uri + scope + response_type`

```
https://accounts.google.com/o/oauth2/auth?client_id=[ShareTube의 client id]&redirect_uri=http://localhost:5000/callback&scope=https://www.googleapis.com/auth/youtube&response_type=token
```

만약 유튜브 구독 목록 외에 다른 데이터를 추가적으로 사용할 예정이라면 여러 개의 scope를 공백으로 구분하여 전달하면 된다.

이제 철수가 로그인 버튼을 클릭하면, 위에서 추가한 정보들과 함께 다음과 같은 로그인 페이지로 연결된다.

<img src="https://user-images.githubusercontent.com/75300807/135041867-e9aa2880-f36f-4d18-8168-f486e120cf37.PNG" width=50%>

철수가 로그인을 완료하면, 구글의 인증 서버는 철수가 들고온 client의 정보 즉, ShareTube의 client id, redirect URI를 검증하여 사전에 정상적으로 등록이 완료된 애플리케이션인지 확인한다. 앞선 준비 단계에서 ShareTube를 구글 클라우드 플랫폼에 잘 등록해뒀기 때문에 이 과정을 통과할 수 있을 것이고, 다음으로는 철수에게 자신의 정보를 ShareTube가 사용하는 것에 대한 허가 여부를 묻는 페이지로 이어진다.

<img src="https://user-images.githubusercontent.com/75300807/135043025-391db714-1de0-4fe2-a0fb-76a7f57124da.PNG" width=50%>

철수가 해당 항목에 체크를 함으로써 구글은 철수라는 사용자가 자신의 정보를 ShareTube가 사용한다는 것에 동의했다는 사실을 기억하게 되고, 이로써 구글은 ShareTube라는 애플리케이션에 철수의 유튜브 구독 목록 정보를 요청할 수 있다는 '자격'을 제공한다. 그 자격이 바로 **Access Token**이다.

## 2. Access Token 발급 받기

ShareTube는 구글의 검증을 통과하고, 철수로부터 동의를 얻은 덕분에 Access Token이 발급받았다. 그렇다면 발급된 Access Token은 어디에 있으며 도대체 어떻게 사용해야 하는 것일까?

다시 앞선 상황으로 돌아가보자. 인증을 끝마친 구글 인증 서버는 철수를 다시 ShareTube로 돌려보내야 할 것이다. 여기에 사용되는 주소가 바로 **Redirect URI**인 것이다. 그리고 구글은 이때 URI의 해시에 access token 정보를 담아서 반환한다. 따라서 철수가 redirect 되는 페이지의 URI는 다음과 같다. (토큰의 값은 aabbcc라고 임의로 가정하자)

```
http://localhost:5000/callback#access_token=aabbcc&token_type=Bearer&expires_in=3600
```

따라서 ShareTube는 `localhost:5000/callback`의 주소에서 `location.hash` 등의 방법을 사용하여 URI에 담긴 access token을 확보할 수 있다.

bearer? expires_in 1시간?

이제 이 토큰을 사용하여 철수의 유튜브 구독목록을 얻기 위한 구글 API를 호출할 일만 남았다.

## 3. Access Token을 사용하여 API 호출하기

access token의 내용을 살펴보면 '토큰의 종류는 Bearer이며, 1시간 뒤에 만료되고, 값은 aabbcc이다' 라는 사실을 알 수 있다. (토큰의 형식 중 하나인 Bearer에 대해서는 따로 살펴보는 것으로..)

이제 획득한 토큰을 사용하여 필요한 데이터를 얻기 위해 API를 호출해야 한다. 토큰을 HTTP request 헤더에 담아서 보내는 방법과 직접 쿼리 매개변수에 담아서 보내는 방법이 있는데, 보안적 측면에서 전자의 방법이 권장된다. (+ RESTful 하지도 않은 방법이다) 따라서 전자의 방법을 사용하기로 한다.

다음으로는 ShareTube가 사용하기로 scope에 등록했던 [YouTube Data API](https://developers.google.com/youtube/v3/docs?hl=ko) 페이지에서 구독 목록 데이터를 응답받을 수 있는 API 주소를 확인한다. (`https://www.googleapis.com/youtube/v3/subscriptions`)

해당 API로 GET 요청을 보낼 때, 쿼리 매개변수로 '데이터를 어떠한 형태로 응답 받을지'를 설정할 수 있다. 필수적으로 설정해야 하는 매개변수, 선택적으로 사용 가능한 옵션이 있으니 설명을 읽어보고 필요에 맞게 고르면 되겠다.

ShareTube에서는 '인증된 사용자 본인의 구독 채널 정보를 한 번에 50개씩 응답 받겠다'라는 목적을 가지고 있기 때문에 다음의 주소로 API 요청을 보낸다.

```
https://www.googleapis.com/youtube/v3/subscriptions?part=snippet&mine=true&maxResults=50
```

그리고 앞서 확인했던 것처럼 GET 요청의 옵션으로는 request 헤더에 다음과 같은 형식으로 토큰을 담는다.

```
headers: {
	Authorization: 'Bearer aabbcc'
}
```

이제 요청에 대한 응답을 콘솔창으로 확인해보자. API 호출 시 설정했던 옵션에 맞는 형태의 JSON 데이터가 응답된 것을 확인할 수 있다.

<img src="https://user-images.githubusercontent.com/75300807/135617373-57e1f9a2-3f75-4096-9faf-4272065a12b8.PNG" width=90%>

이로써 ShareTube는 OAuth 2.0 인증을 통해 철수의 권한을 위임 받은 뒤, 구글 API를 사용해 철수의 유튜브 구독 채널 정보를 성공적으로 확보할 수 있었다.
