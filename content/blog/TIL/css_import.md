---
title: "[TIL] <link>와 @import"
date: "2021-06-04"
tags: ["TIL", "HTML", "CSS"]
---
프로젝트에서 페이지 별 CSS 파일에 공통 요소를 적용하기 위해 ```@import```를 사용해 default.css 파일을 불러오려고 했다. 그러다 궁금한 점이 생겼다. a.css 파일 안에 b.css 파일을 import 하는 방식과, HTML 파일에 ```<link>``` 태그를 사용하여  a.css 파일과 b.css 파일을 모두 불러오는 방식의 차이는 뭘까? 조금 오래된 글이긴 하지만 각각의 차이를 설명한 글이 있어서 읽고 정리해본다. [👉원문 보기](https://www.stevesouders.com/blog/2009/04/09/dont-use-import/)  

결론을 먼저 말하자면, ```@import```를 사용하지 말고 ```<link>``` 태그를 사용하라는 것. 그 이유를 살펴보자.



#### HTML 파일에 CSS 파일을 불러오는 방법

먼저 HTML 파일에 CSS 파일을 불러오는 방법은 크게 두 가지로 나뉜다.

첫 번째는 HTML link 태그를 사용하는 방법,

```html
<link rel='stylesheet' href='a.css'>
```

두 번째는 HTML style 태그 내부에 @import를 사용하는 방법이다.

```html
<style>
@import url('a.css');
</style>
```

이 두 가지 방법을 전제로 link 태그와 @import를 다양한 방식으로 조합하여 사용할 때의 차이점을 살펴본다.



#### [1] @import & @import

```html
<style>
@import url('a.css');
@import url('b.css');
</style>
```

\<style> 태그 내부에 @import를 각각 사용하여 a.css와 b.css를 불러오는 방식이다. 이 경우, 페이지가 요청되면 두 css 파일은 병렬적(parallel)으로 다운로드 된다. 즉 성능적으로 손해를 보지 않는다.



#### [2] \<link> & @import

```html
<link rel='stylesheet' type='text/css' href='a.css'>
<style>
@import url('b.css');
</style>
```

\<link> 태그와 @import를 섞어서 쓰는 경우, 두 파일은 순차적(sequentially)으로 다운로드 된다. (원문의 테스트는 IE 6, 7, 8을 기준으로 진행되었다) 따라서 두 파일을 동시에 다운로드 하는 [1]번 방식보다 시간적으로 손해를 보게 된다. 



#### [3] @import in \<link>

```html
<!-- in the HTML document -->
<link rel='stylesheet' type='text/css' href='a.css'>

<!-- in a.css -->
@import url('b.css');
```

이번엔 내가 시도하려던 방식이다. link 태그로 a.css 파일을 불러오는데 a.css 파일 내부에 @import를 사용해 b.css 파일을 포함시킨 방식이다.

이 경우 [2]번과 마찬가지로 파일을 순차적으로 다운로드 한다. (모든 브라우저에서 그렇다고 한다..) 사실 논리적으로 생각해보면 당연한 일이다. 브라우저는 a.css를 우선 다운로드 한 뒤, parse 하는 과정에서 a.css가 b.css를 import 한다는 사실을 알게되어 그제야 b.css를 다운받기 시작할 테니까.

나는 가장 성능이 안 좋은 방식을 사용하려고 한 것이다.



#### [4] \<link> & \<link>

```html
<link rel='stylesheet' type='text/css' href='a.css'>
<link rel='stylesheet' type='text/css' href='b.css'>
```

\<link> 태그를 여러 개 사용하여 각 파일을 불러오는 방식이다.  \<link> 태그를 사용하면 모든 브라우저에서 파일을 병렬적으로 다운로드 한다. 또한 \<link> 태그를 사용하면 파일의 다운로드 순서를 보장받을 수 있다. ([1]번처럼 다수의 @import를 사용하는 경우 브라우저 및 상황에따라 다운로드 순서가 보장되지 않는 경우도 있다고 한다)



#### 결론

@import를 사용하면 경우에 따라 시간적으로 손해를 볼 여지가 있다. 특히 IE에서는 다운로드 순서를 보장 받지 못하여, \<link>와 @import를 섞어 쓰면 다운로드 시간이 오래 걸리는 파일을 다운받는 동안 다른 파일들이 블록되어 더 많은 시간이 소비되는 경우도 발생한다.

**\<link>** 를 사용하자. 