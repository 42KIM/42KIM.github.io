---
title: "[알고리즘] 정렬 - 가장 큰 수"
date: "2021-04-24"
tags: ["알고리즘", "코딩테스트"]
---
[프로그래머스 Level 2 정렬 문제](https://programmers.co.kr/learn/courses/30/lessons/42746)

#### 문제 설명

0 또는 양의 정수가 주어졌을 때, 정수를 이어 붙여 만들 수 있는 가장 큰 수를 알아내 주세요.

예를 들어, 주어진 정수가 [6, 10, 2]라면 [6102, 6210, 1062, 1026, 2610, 2106]를 만들 수 있고, 이중 가장 큰 수는 6210입니다.

0 또는 양의 정수가 담긴 배열 numbers가 매개변수로 주어질 때, 순서를 재배치하여 만들 수 있는 가장 큰 수를 문자열로 바꾸어 return 하도록 solution 함수를 작성해주세요.

#### 제한 사항

+ numbers의 길이는 1 이상 100,000 이하입니다.
+ numbers의 원소는 0 이상 1,000 이하입니다.
+ 정답이 너무 클 수 있으니 문자열로 바꾸어 return 합니다.

#### 입출력 예

| numbers            | return    |
| ----------------- | --------- |
| [6, 10, 2]        | "6210"    |
| [3, 30, 34, 5, 9] | "9534330" |



#### 풀이

일단 나는 배열 각 요소의 맨 앞자리 수를 기준으로 내림차순 정렬을 하려고 했다. 그 경우, 앞자리 숫자가 같을 때 그 다음 자리수를 반복하여 계속 비교해야한다. 만약 두 요소의 자리수가 다른 경우 비교하는 방법은 더 복잡해진다. 문자열의 형태로 이어붙였을 때 어떻게 해야 더 큰 수가 나오게끔 정렬할 수 있을까. 도저히 방법을 찾지 못해 구글링해보니 매우 매우 간단한 방법으로 해결할 수 있었음..  

문자열 형태로 이어붙였을 때 커지는 숫자를 만들어야하는 게 이 문제의 핵심이기 때문에, 두 요소의 비교 또한 문자열 형태로 이어붙진 다음에 큰 순서대로 정렬하면 되는 것이었다. 두 수의 크기를 비교하는 아이디어, 배열의 sort() 메서드 작동원리를 잘 이해하고 있는 것이 중요했다. 풀이 순서는,  

1. number 타입의 요소들을 string 타입으로 변환 후,
2. 요소를 앞뒤로 더했을 때, 큰 값을 만들어내는 순으로 정렬한다.
3. 배열의 요소를 하나의 문자열로 합친다.

  

**그런데**, 테스트 케이스 하나를 통과하지 못했다. 문제를 다시 잘 읽어보면, 모든 요소가 0인 경우도 가능하다. 이때 answer는 '0000..'이 반환되기 때문에 이 부분도 처리해줘야 한다.



#### 코드

```javascript
function solution(numbers) {
    var answer = '';
    
    answer = numbers.map(el=>el.toString())
                    .sort((a,b)=>(b+a)-(a+b))
                    .join('');
    if(answer[0]==='0') return '0';
    return answer;
}
```



프로그래머스 정답 풀이를 보니 신박한 방법들이 많다.  
숫자를 문자열로 바꿀 때 ```el + ''```를 사용하거나 sort() 함수 내에서 a,b를 ```템플릿 리터럴```로 감싸는 방법,  
마지막 0을 처리할 때 ```삼항연산자```, ```정규표현식```을 사용하는 방법 등등

  

  

갈 길이 멀다.