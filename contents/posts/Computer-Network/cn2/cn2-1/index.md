---
title: "5계층 [애플리케이션 계층] part1"
description: "5계층 [애플리케이션 계층] part1"
date: 2023-07-27
update: 2023-07-27
tags:
  - 컴퓨터네트워크

series: "컴퓨터네트워크"
---

## 네트워크 애플리케이션
앞 선 포스팅에서 말한 것처럼 애플리케이션 계층은 `카카오톡` 같은 애플리케이션이나 `웹 브라우저` 등과 같은 사용자가 실제로 사용하는 어플리케이션들이 이 계층에 속합니다.
여기에는 크게 잘 알려진 두 가지 구조가 존재합니다.
- `클라이언트-서버 구조`와 `P2P구조`입니다.
### 클라이언트-서버구조 :
- 여기서 항상 동작하고 있는 호스트를 `서버`라고 하고 이 서버는 `클라이언트` 라는 다양한 호스트들로부터 많은 요청을 받게 됩니다.
- 당연하겠지만 클라이언트끼리는 직접적으로 통신을 하지 않습니다.
- `서버가 잘 알려진 IP주소를 가집니다`
- 사이트가 인기가 있으면 서버가 신속하게 처리하지 못할 수 있습니다. 따라서 대기업들은 많은 수의 호스트를 갖춘 `데이터 센터를 서버로 사용`하고 있습니다.
### P2P구조:
- 이건 토렌트생각하시면 이해가 좀 쉬울 겁니다!
- P2P 구조에서는 항상 켜져있는 인프라스트럭처서버에 최소로의존한다(혹은전혀
의존하지않는다) -> 즉, `중앙서버에 약간 의존하거나 아예 의존하지 않는다`는 말입니다 
- 대신에 피어(peer)라는 간헐적으로 연결된 호스트쌍이 서로 직접 통신하게되는데 이 `서버 안끼고 피어들끼리 통신한다고 해서` `peer to peer` -> `P2P` 라고 부르게 된 것입니다. (토렌트를 사용해 보셨으면 감이 오시겠지만 이 피어는 그냥 우리같은 일반인들의 pc라고 생각하면 편합니다.)
- 특징으로는 `자가 확장성(self-scalability)가 있습니다. 
>scalability(확장성)이란? 
-> 처음에 확장성이라길래 뭔가 사이즈가 커지는 느낌만을 생각했는데 저의 오개념이었습니다.
->`많은 사용자에서도 잘 돌아갈 뿐만 아니라, 사용자 수가 적으도 그 만큼 서버 자원을 아낄 수 있어야 scalability가 있다고 할 수 있습니다`
따라서 자가 확장성이 특징이라고 한다면 `시스템 전체적으로 참가자의 추가나 제거에 유연하게 대응하는 능력을 의미한다` 라고 보는게 더 정확합니다.

- 즉, 내가 어떤 파일을 다운받으면 내가 하나의 서버가 되고 다른 사람이 또 그 파일을 다운받을 수가 있죠? 
근데 내가 사라진다고 해서 이 시스템이 없어지는 건 아니잖아요? 
누군가는 또 다운을 받아놓아서 서버의 역할을 하고 있으니까 말이죠!
- 마지막으로 P2P구조는 비용효율적이에요. 서버인프라스트럭처와 서버대역폭을 별로 요구하지않기 때문입니다!

## 프로세스 간 통신
- 네트워크 애플리케이션을 개발하기전에 호스트에서 실행하는 프로그램이 서로 통신하는 법을 알아야 합니다.
- 프로그램이 실행되면 프로세스라고 부르는데 호스트들은 이 프로세스를 통해 통신을 합니다.
- 프로세스들은 메시지교환으로 서로 통신함
- 프로세스는 소켓을 통해 메시지를 보내고 받는다
- 비유하자면 프로세스는 집 / 소켓은 출입구
![](https://velog.velcdn.com/images/97gkswn/post/2c34f995-d601-40ab-886f-002434a93f9d/image.png)

### 소켓이란?
위와 같이 `소켓은 애플리케이션계층과 트랜스포트계층간의 인터페이스입니다.`
소켓그림이 약간 걸쳐져 있는 것을 볼 수 있는데 그래서 애플리케이션 개발자는 소켓의 애플리케이션 계층은 다 통제할 수 있는데 소켓의 트랜스포트 계층은 통제를 못한다고 보면됩니다. 통제를 해도 (1)트랜스포트프로토콜의선택,(2) 최대버퍼와 최대세그먼트크기 같은 약간의 트랜스포트계층의 매개변수 설정정도를 할 수 있다고 하네요.

### 패킷에 포함돼야하는 정보 (IP주소, PORT번호)
- 어떤 호스트의 프로세스가 패킷을 보내려고 하면 두 가지 정보가 명시되어야합니다 (1) 보내는(송신) 호스트의 주소, (2) 받는(목적지) 호스트내의 수신프로세스를 명시하는 식별자
- (1) 보내는(송신) 호스트의 주소는 IP주소를 사용하고
- (2) 목적지 호스트내의 수신프로세스를 명시하는 식별자는 포트(Port)번호를 사용합니다. 그래서 받는 호스트쪽에서 어떤 프로세스가 이 패키을 받을지를 판단하는거죠! ex) 웹 서버 포트번호는 80, 메일 서버는 포트번호 25

## 애플리케이션의 메시지를 어떤 트랜스포트(4계층) 방법으로 보내지?

애플리케이션(5계층)에서 메시지를 보내려고 하면 소켓 반대편에 위치한 트랜스포트계층(4계층)에서는 어떤 방법을 통해서 수신호스트의 프로세스 소켓에 저 메시지를 보낼지를 결정해야 합니다. 
`소켓은 애플리케이션계층과 트랜스포트계층간의 인터페이스임`
이 때 트랜스포트쪽에서도 프로토콜(메시지를 보낼 방법)이 여러 개 있겠죠?
그 여러 개 중에서 뭘 고르는지는 아래의 4가지를 고려하여 결정합니다.
### 다음의 4가지 속성을 고려하여  방법을 결정함 

#### 신뢰적 데이터 전송
뭔말이냐면 패킷이 손실될 수도 있는데 그걸 감안해 줄 수 있냐 아니면 절대 손실되면 안되냐를 기준으로 애플리케이션을 나눌 수 있다는 말입니다.
##### 패킷 손실 용납 못하는 애플리케이션
- 전자메일,파일전송,원격호스트접속,웹문서전송,재무애플리케이션과 같이 내용(패킷)이 절대 손실되면 안되는 애플리케이션들 
##### 약간의 손실은 괜찮은 애플리케이션
- 오디오/비디오 같은 멀티미디어 애플리케이션 같은 경우에는 손실을 약간을 감안할 수 있는 애플리케이션들
#### 처리율
처리율은 송신프로세스가 수신프로세스에게 전달할 수 있는 비트의 비율을 의미합니다.
대역폭에 따라서 처리율이 달라지는데 여기에 민감하냐 덜민감하냐로 애플리케이션을 크게 두 가지로 나눠서 고려할 수 있음
##### 매우민감한 - 대역폭 민감 애플리케이션(bandwidth-sensitiveapplication)
- 여기에는 대부분의 애플리케이션들이 속함
##### 덜 민감한 - 탄력적 애플리케이션(elastic application)
- 여기에는 전자메일, 파일전송 등 웹 전송이 융통성있는 애플리케이션들이 속함

#### 시간
인터넷전화, 가상환경, 원격회의, 게임 이런 것들은 진짜 송신자가 보내는 모든 비트가 수신자의 소켓에 100ms 안에 와야하지 않겠나요?
##### 무조건 빨리 와야 하는 애플리케이션
##### 조금 늦어도 괜찮은 애플리케이션

#### 보안
송신측 프랜스포트 프로토콜은 모든 데이터를 암호화해서 보낼 수 있고 수신호스트에서 트랜스포트 프로토콜은 그 모든 암호화데이터들을 해독할 수 있습니다.
그니까 보안이 중요한 애플리케이션이냐 아니냐로 또 나눌 수 있겠습니다.
##### 보안이 중요한 애플리케이션
##### 보안이 상대적으로 덜 중요한 애플리케이션