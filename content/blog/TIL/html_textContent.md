---
title: "[HTML/CSS] textContent / innerText / innerHTML 차이"
date: "2021-07-05"
tags: ["HTML", "CSS"]
---
HTML element의 텍스트를 가져오거나, 또는 텍스트를 추가해야 하는 경우가 종종 생긴다. 이를 수행할 수 있는 몇 가지 방법이 있다. ```textContent```, ```innerText```, ```innerHTML```의 차이점을 알아보고 무엇을 사용하는 것이 좋을지 알아보자.

HTML이 다음과 같이 작성되었을 때, 각각 어떤 차이를 보일까.

```html
<div>
    Do
    you   see <span style="display: none">the difference?</span>
</div>
```



### innerHTML

```javascript
const div = document.querySelector("div");

console.log(div.innerHTML);
// Do
// you   see <span style="display: none">the difference?</span>
```

```innerHTML```을 사용해 div 요소에 접근하면, div 태그 내부에 작성한 모든 내용을 토씨 하나 틀린 것 없이 그대로 가져온다. 해당 태그 내부에 작성된 '모든 HTML 마크업'을 문자열 형태로 긁어오는 것이다.

반대로 innerHTML을 사용해 요소에 내용을 설정할 때는 HTML이 파싱된 결과가 화면에 출력된다.

```javascript
div.innerHTML = '<span style="color: red">hello world</span>';
```

위와 같은 코드가 작성되었다면, 화면에는  

<span style="color: red">hello world</span>  

가 출력되는 것이다.

이 말인 즉슨, innerHTML을 사용하는 동안 XSS 공격을 당할 경우 작성자가 원치 않는 HTML 마크업이 스크립트에 추가 또는 삭제될 수 있다는 위험성이 있다는 것을 의미한다.  



또한 HTML 파싱에 소요되는 비용이 성능에 영향을 끼친다.



### innerText

```javascript
const div = document.querySelector("div");

console.log(div.innerText);
// Do you see
```

```innerText```를 사용하여 div 요소에 접근하면, 브라우저에 출력되는 것과 동일한 결과물을 가져온다. 즉, 사용자에게 '보여지는 텍스트' 값을 읽어오는 것이다. (사용자가 화면을 드래그하여 복사+붙여넣기 한 결과와 동일한 것)

임의의 줄바꿈과 3칸의 공백은 한 칸의 공백으로 처리되었고, display: none이 적용되어 화면에 보이지 않는 텍스트도 가져오지 않는다. 즉, innetText는 CSS까지 반영된 최종 렌더링 결과를 가져온다는 것을 알 수 있다.

또한 innerText를 사용하여 값을 설정하는 경우에는 렌더링 된 최신 값을 계산하여 반영하기 위한 ```Reflow```가 발생하게 되어 화면의 깜빡거림이 발생하게 되고, 당연히 성능도 그만큼 떨어질 수밖에 없는 것이다.  
(과거 버전의 IE 브라우저에서는 textContent를 지원하지 않았기 때문에 innerText는 IE 브라우저에 적합하게 만들어졌다고 한다. 따라서 IE 환경에서는 innerText의 성능이 가장 좋다고 한다. 하지만 IE는 죽었어..)



### textContent

```javascript
const div = document.querySelector("div");

console.log(div.textContent);
// Do
// you   see the difference?
```

```textContent```는 innerHTML과 innerText의 중간쯤이라고 보면 된다.

innerHTML과 마찬가지로 공백이나 줄바꿈을 작성된 상태 그대로, display가 none인 자식 태그의 내용도 가져오지만, 태그를 제외한 오로지 '텍스트' 값만 골라서 가져오기 때문이다.

반면 innerHTML처럼 HTML로 분석해야 하거나, innerText처럼 렌더링 이후의 값을 계산하지 않기 때문에 셋 중에서 가장 성능이 좋다.



이처럼 각각의 특징을 고려하여  
HTML 마크업 원본의 구성을 파악하고 싶다면 innerHTML,  
화면에 렌더링 되는 텍스트만 확인하고 싶다면 innerText,  
렌더링된 텍스트에 어떤 스타일이 적용되었는지를 확인하려면 textContent를 사용하는 식으로 상황에 맞게 사용하면 좋을 것 같다.



#### 참고

[MDN Web Docs](https://developer.mozilla.org/ko/docs/Web/API/Node/textContent)  
[What’s Best: innerText vs. innerHTML vs. textContent](https://betterprogramming.pub/whats-best-innertext-vs-innerhtml-vs-textcontent-903ebc43a3fc)