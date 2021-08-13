---
title: "네트워크 장비 (Network Devices)"
date: "2021-08-13"
tags: ["TIL", "네트워크"]
---
OSI 7 Layer를 학습하면서 이론적으로는 각 계층이 어떠한 역할을 담당하는지 알 수 있었다. 그러나 네트워크라는 과목을 처음 접하다보니 개념화된 각 계층의 실질적인 프로세스가 어디서, 어떻게 진행되는지를 이해하지 않고서는 OSI 7 Layer를 이해하는 데에 어려움이 있었다.

일부 계층의 역할을 수행하는 실체가 존재하는 네트워크 장비에 대해 알아보면 그 과정을 구체화하는 데에 조금이나마 도움이 되지 않을까 생각하여, 네트워크 장비들에 대해 알아보았다.



### 네트워크 흐름

<img src="https://user-images.githubusercontent.com/75300807/129347333-550a2054-d207-4fcf-a529-ae9168267bdf.png" style="zoom:65%;" />

먼저 통신이 발생하는 흐름에 대해서 간략히 살펴보자.

사용자가 자신의 PC에서 특정 웹사이트를 접속하기 위해 주소창에 주소를 입력한 상황을 예로 들어보자. 사용자의 GET 요청은 응용계층에서 시작하여 하위 계층을 거치며 캡슐화될 것이다. 최하위 계층인 물리 계층에서는 데이터가 전기 신호로 변환된다. 이때, 메인보드의 ```PHY 칩```이라는 하드웨어가 데이터를 전기 신호로 변환시키는 역할을 수행한다.

변환된 전기 신호는 사용자가 속한 네트워크의 스위치를 거치고, 라우터를 거쳐서 웹 서버가 속한 '다른 네트워크'의  라우터로 전달된다. 여기서부터 다시 역순으로 웹 서버가 위치한 PC에 도달하면 물리 계층에서 전기 신호가 다시 데이터로 변환되고, 7계층의 역캡슐화 과정을 거쳐서 비로소 요청이 전달될 것이다.

이 과정에서 네트워크 장비들은 물리 계층, 데이터 링크 계층, 네트워크 계층에 해당되는 역할을 수행한다. 그 부분을 염두해두자.



### LAN 통신에 이용되는 장비

리피터, 허브, 스위치는 로컬 네트워크에서의 통신 장비, 즉 사무실이나 가정 LAN에서 역할을 수행하는 장비다. 외부 네트워크와의 통신은 불가능한 상태다.

리피터와 허브는 이제 거의 사용되지 않으니 그냥 이런 게 있었다 정도만 확인해두자.

#### 리피터 Repeater

<img src="https://user-images.githubusercontent.com/75300807/129363220-0d7a31d6-afe0-4822-bd59-28da0f3d8435.jpg" style="zoom:85%;" />

리피터는 전기 신호가 노이즈에 의해 손상을 입는 경우 신호를 복원시켜주거나, 신호의 도달 범위를 늘리기 위해서 신호를 증폭시켜주는 장비다. 어떤 데이터나 경로 식별 같은 논리적 작업 없이, 말 그대로 신호를 정형하고 연장시켜주는 보조도구인 것이다. 이제는 다른 장비들도 이 기능을 지원하기 때문에 더 이상 필요가 없어졌다.

#### 허브 Hub

<img src="https://user-images.githubusercontent.com/75300807/129361680-730c8711-2b6c-434d-9875-1346df6da8c5.PNG" style="zoom:45%;" />

허브는 동일 네트워크 내에 있는 컴퓨터들을 LAN 케이블을 통해 서로 연결하여 통신할 수 있도록 돕는 장비이다. 허브를 통해서 LAN 통신이 가능하게 되는 것이다. 

허브에 연결되어 있으면 1대 多 통신이 가능해진다. 그러나 허브의 통신 방식에는 문제가 있다. 어떤 컴퓨터가 LAN에 있는 특정 컴퓨터 한 대에만 데이터를 전송하려고 해도, 해당 허브에 연결된 모든 컴퓨터에 일괄적으로 데이터가 전송된다. 때문에 LAN의 모든 컴퓨터는 자신에게 온 요청이 맞는지 일일이 검증하는 과정이 필요하다. 비효율적인 통신이 발생하는 것이다.

#### 스위치 Switch

![network_switch](https://user-images.githubusercontent.com/75300807/129364651-8b5391e8-2c7b-45d6-9a5b-4dd14a8c220c.gif)

허브의 문제를 보완한 것이 스위치이다. 스위치의 역할은 허브와 비슷한데, 원하는 대상에게만 데이터를 전송할 수 있게 된 것이다. 이는 MAC 주소라는 것 덕분에 가능하다. 간단히만 언급하자면..

모든 랜 카드에는 고유의 MAC 주소가 지정되어 있다. 이 덕분에 출발지에서는 자신의 MAC 주소와 도착지의 MAC 주소를 데이터에 함께 담을 수 있다. 이를 물리 계층으로 전달하여 전기 신호로 변환되면 스위치로 보내진다. 스위치에는 MAC 주소 테이블이 있다. MAC 주소 테이블에 의해 MAC 주소를 등록하고, 식별할 수 있다. 따라서 목적지에 해당되는 MAC 주소를 가진 컴퓨터에게만 데이터를 전송할 수 있게 해주는 것이다.

스위치가 데이터 링크 계층에 속하는 이유는 데이터에 MAC 주소가 캡슐화되는 과정이 데이터 링크 계층에서 수행되기 때문이다. 마찬가지로 역캡슐화도 데이터 링크에서 수행된다.

이렇게 스위치를 사용하여 동일 네트워크에 속한 컴퓨터들이 통신할 수 있는데, 사실 더욱 중요한 건 외부 네트워크와의 통신일 것이다. 스위치는 LAN의 컴퓨터들을 연결하는 장비라고 했으니, 이제 스위치와 또 다른 스위치를 연결해줄 수 있는 역할을 수행할 장비가 필요하다.



### 외부 네트워크와의 통신에 이용되는 장비

#### 라우터 Router

![router](https://user-images.githubusercontent.com/75300807/129361620-b1b4a27a-d6d9-4ce6-8e94-c61a4443a7e1.jpg)

라우터는 LAN이 아닌 다른 네트워크와의 통신을 가능하게 하는 장비이다. 즉, 현재 네트워크의 라우터에서 다른 네트워크의 라우터로 데이터를 전송하게 해주는 것이다.

라우터가 네트워크 계층에 속하는 이유는 IP주소 정보를 이용하여 외부 네트워크와의 연결을 가능하게 해주기 때문이다. 네트워크 계층의 캡슐화 과정에서 데이터에 IP 정보가 포함된다. 라우터에는 스위치의 MAC 주소 테이블과 비슷한 역할을 하는 '라우팅 테이블'이 있어서, 마찬가지로 출발지와 도착지의 IP 주소를 관리하여 통신이 필요한 두 네트워크를 연결할 수 있는 것이다. 이렇게 IP주소를 사용하여 어떤 경로를 통해 데이터를 가장 빠르게 보낼지를 결정하는 것을 '라우팅'이라고 한다. (라우팅에 대해서는 추후 알아보기로)



### 정리

마지막으로 내용을 되짚어 보면,

<img src="https://user-images.githubusercontent.com/75300807/129347333-550a2054-d207-4fcf-a529-ae9168267bdf.png" style="zoom:65%;" />

7계층의 캡슐화 과정을 거쳐 물리 계층에 도달한 데이터는 PHY 칩에 의해 전기 신호로 변환된다. 전기 신호는 데이터 링크 계층의 스위치로 전송되고, 스위치는 MAC 주소 테이블을 통해 MAC 주소를 식별하여 동일한 네트워크 내에서의 통신을 가능하게 해준다.

외부 네트워크와의 통신이 필요하다면 전기 신호는 라우터로 전송이 된다. 우선 MAC 주소를 식별한 뒤, 한 계층 더 들어가 IP 주소를 식별하는 네트워크 계층의 작업까지 수행이 되는 것이다. 데이터는 다시 캡슐화 과정을 거쳐서 목적지 네트워크의 라우터로 전달되어 데이터가 외부 네트워크에 송신될 수 있게 되는 것이다. 

즉, 스위치는 동일 네트워크에 속한 컴퓨터끼리의 통신을 중계하는 장비이고, 라우터는 외부 네트워크와의 통신을 중계하는 장비인 것이다. 실제로는 스위치와 라우터의 역할이 하나의 장비에 의해 수행되는 경우도 많다는 사실도 알아두면 좋을 것 같다.



#### **이미지 출처**

[Network devices - computer network](https://www.brainkart.com/article/Network-devices_36829/)  
[What is hub in Networking](https://www.learnabhi.com/hub/)  
[Network Switch : Purpose and Functions](http://www.yourownlinux.com/2013/07/network-switch-purpose-and-functions.html)  
[Backbone network components](http://what-when-how.com/data-communications-and-networking/backbone-network-components-data-communications-and-networking/)  