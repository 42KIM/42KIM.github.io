---
title: "Gatsby로 Github 블로그 만들기"
date: "2021-04-20"
tags: ["tutorial", "gatsby"]
---
### 0.

중구난방으로 보고 듣고 읽는 것들을 조금이라도 붙잡아두기 위해 제대로 된 기록의 필요성을 느낀다. 어디에 기록해야할지, 시작부터 선택의 기로에 놓였다. 결론적으로는 블로그 글 하나 쓰는 데도 커밋과 푸시를 반복해야하는 귀찮음보다 깃허브와 외부 블로그를 이원화하여 관리하는 것이 더 귀찮을 것 같았다. 그리고 기왕이면 나의 산물(?)들을 한 곳에서 모아보면 더 뿌듯할 것만 같다는 판단 아래 이래 저래 검색해보다 Github 블로그 용으로 많이 쓰인다는 Gatsby를 골랐다. 근데 Gatsby가 도대체 뭐지?



### 1.

[Gatsby Documentation](https://www.gatsbyjs.com/docs/)을 읽어봤다. 영어다. 설명에 따르면,
> Gatsby는 웹사이트와 앱을 만들기 위한 React 기반의 오픈 소스 프레임워크이다. 포트폴리오 사이트 혹은 블로그를 만들거나 기업 홈페이지, 높은 트래픽의 이커머스 스토어 등에 적합하다. 
라고 한다. 아직은 잘 모르겠지만 좋다고 하니 좋은 것 같다. 튜토리얼을 따라가보기로 한다.



### 2.

개발 환경 구축을 위해 먼저 ```Node.js```의 설치를 요구한다. Gatsby는 ```JavaScript```를 사용하여 만들어졌기 때문에 Node.js 런타임을 필요로 한다. Node.js를 설치하면 ```npm```도 따라온다.
다음으로 필요한 것은 ```Git```의 설치다. Gatsby-starter-site를 설치하면 Git을 이용해 필요한 파일들을 다운로드하고 설치하게 된다.



### 3.

새로운 Gatsby 사이트를 생성하고 개발을 돕는 ```Gatsby CLI tool```을 사용하려면, ```npm```을 통해 설치해야 한다. 터미널에서 아래 명령을 실행한다.
```nodejs
npm install -g gatsby-cli
// gatsby --help 명령을 통해 다양한 도움을 받아보자...!
```



### 4.

이제 Gatsby CLI tool을 사용해 새로운 사이트를 생성해보자. 기본 구성이 갖춰진 틀인 ```starter```가 제공된다. ```gatsby-starter-default```, ```gatsby-starter-blog``` 등 용도에 맞는 몇 가지 기본 starter가 존재한다. 이를 설치해 본인이 직접 커스터마이즈 해도 되고, 다른 사람들이 만들어둔 테마 중 마음에 드는 것을 골라 사용해도 된다.  

새로운 starter를 생성하는 명령은 다음과 같다.
```nodejs
gatsby new "생성할 프로젝트 디렉토리 이름" "사용하고자 하는 starter의 GitHub URL"
```
(URL부분을 생략하면 default-starter가 생성된다) 이렇게 선택한 starter 설치를 완료했다면,
```nodejs
cd "바로 위에서 생성한 디렉토리 이름"
```
명령을 통해 해당 디렉토리로 이동한다. 프로젝트에 대해 어떠한 명령을 하고자 할 때는 언제나 이처럼 해당 디렉토리에 위치한 상태여야 한다.  



### 5.

이제 개발 서버를 구동시켜보자.
```nodejs
gatsby develop
```
이 명령은 개발 서버를 시작시켜준다. 이제 ```http://localhost:8000/``` 주소를 통해 내 블로그의 개발자 환경으로 접속할 수 있다. 물론 아직 인터넷에 배포된 상태가 아니라, 나만 볼 수 있는 로컬 환경에서의 접속이다. 이제 Gatsby를 통해 생성한 내 블로그가 실제로 어떤 모습을 하고 있는지 확인할 수 있게 된 것이다.  

만약, 개발 서버 구동을 중단하고 싶다면 터미널에서 ```ctrl + c``` 를 입력하면 된다. 다시 시작하고 싶다면 위에서 했던 것처럼 gatsby develop 명령을 실행하면 된다.

  

### 중간점검.

이로써 Gatsby를 사용해 블로그를 생성했다. 생성**만** 했다.  
커스터마이즈를 위해 블로그의 내/외부 구조를 수정하는 것은 React를 공부한 뒤에 시도해볼 예정.
우선은 블로그에 포스팅을 하는 방법까지만 알아본다.  



### 6.

블로그 포스팅에는 ```Markdown``` 파일을 사용하려고 한다. [👉마크다운 작성법](https://www.gatsbyjs.com/docs/how-to/routing/adding-markdown-pages/)
Gatsby가 마크다운 파일을 읽고 해석하기 위해서는 두 가지 플러그인이 필요하다.
```gatsby-source-filesystem``` 플러그인은 filesystem으로부터 파일을 읽어들이고, ```gatsby-transformer-remark``` 플러그인이 마크다운을 parse하여 HTML로 변환시켜준다.
나의 경우 gatsby-starter-blog를 설치했기 때문에, 해당 플러그인들이 이미 포함되어 있다. Default 스타터를 생성한 경우 npm을 통해 설치가 필요한 것으로 보인다.



### 7.

VS Code에서 프로젝트 폴더를 열어보자. 나의 경우 blog-starter를 생성했기 때문에 default-starter와는 이미 조금 다른 구조를 가지고 있다. 특히 ```content\blog``` 디렉토리가 생성되어 있는데 여기에 마크다운 파일을 추가하면 바로 포스팅되도록 준비되어 있다.

포스팅에 앞서 임의로 생성된 블로그 이름과 소개를 변경해줄 필요가 있다. 이를 위해 ```gatsby-config.js``` 디렉토리를 살펴본다. 맨 위에서부터 살펴보면 ```siteMetadata```라는 key가 있는데 여기에 해당하는 ```title```, ```author```, ```description``` 등등의 값들을 수정하면 블로그의 기본 정보를 변경할 수 있다.



다시 ```content\blog``` 디렉토리로 돌아와 보면, 디폴트로 생성된 몇 개의 마크다운 파일과 이미지가 존재하는 것이 보인다. 여기에 내가 작성한 마크다운 파일을 추가하기만 하면 블로그에 글이 포스팅된다.

**이때**, 마크다운 파일에 포함되어야 하는 ```metadata```가 있다.
```markdown
---
title: 제목
data: 날짜
---
포스팅 내용 blah blah
```
위와 같이 3개의 대시로 감싼 블록 내의 key, value 쌍을 ```frontmatter```라고 부른다. 마크다운 파일에 포함될 이 블록은 앞에 언급했던 transformer 플러그인에 의해 frontmatter로 parse된다. frontmatter는 데이터베이스에 파일에 대한 추가적인 정보를 전달하는 기능을 담당한다. (지금은 ```GraphQL```과 ```React``` 둘 다 모르니 우선 넘어가도록 한다..)



### 8.

블로그를 생성했고 포스팅 방법을 알아보았으니, 마지막으로 배포가 남아있다.
Gatsby를 선택했던 주요한 이유 중 하나인 Gihub Pages를 통해 블로그를 호스팅하는 방법을 알아보자.
_단, 깃헙 페이지를 통해 배포하는 방법은 repository setting에 따라 조금씩 다르다_.  
나는 ```username.github.io```  경로를 사용하는 방식에 맞게 배포할 것이기 때문에 이에 해당하는 방법을 따른다.



먼저 npm을 통해 ```gh-pages``` 패키지를 설치한다.
```nodejs
npm install gh-pages --save-dev
```
username.github.io 경로로 배포하는 경우, 블로그는 ```master``` 브랜치에 ```push``` 되어야 한다. 이는 깃헙 페이지가 master 브랜치를 배포하기 때문이다. 나는 master 브랜치에서 블로그를 개발하고 있었기에 다음의 두 가지 방법 중 하나를 선택해야 한다.

+ 디폴트 브랜치를 master에서 새로운 브랜치로 바꾸고, master 브랜치를 배포용 디렉토리로만 사용한다.
+ username.github.io를 배포용도로만 사용하고, 소스코드를 위한 새로운 repository를 생성한다.

나는 첫 번째 방법을 택하여 ```source```라는 새로운 브랜치를 생성했다.
```nodejs
git checkout -b source master
```
그리고 package.json 파일에 다음과 같은 deploy script를 추가한다.
```nodejs
{
  "scripts": {
    "deploy": "gatsby build && gh-pages -d public -b master"
  }
}
```
마지막으로 master 브랜치를 배포시켜주자.
```nodejs
npm run deploy
```



### 9.

이제 ```username.github.io``` 로 접속하면 나의 Gatsby 블로그가 배포된 것을 확인할 수 있다.

