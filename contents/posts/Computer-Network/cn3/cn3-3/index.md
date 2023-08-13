---
title: "4계층 [트랜스포트 계층] part3 신뢰적인 데이터 전송 프로토콜"
description: "4계층 [트랜스포트 계층] part3 신뢰적인 데이터 전송 프로토콜"
date: 2023-08-06
update: 2023-08-06
tags:
  - 컴퓨터네트워크

series: "컴퓨터네트워크"
---

## 들어가기 전에
모든 네트워킹에서 근본적으로 가장 중요한 문제가 신뢰적 데이터 전송일 것입니다. 

>
RDT(reliable datatransfer protocol)
- 트랜스포트 계층에서는 신뢰성 있는 데이터교환을 하고싶지만 그 하위계층에서는 신뢰성을 보장할 수 없기 때문에 트랜스포트 계층에서는 RDT프로토콜을 이용합니다.

완전히 신뢰적인 데이터 전송 프로토콜에 도달하기 위해 조금씩 더 복잡해지는 일련의 프로토콜들을 알아볼 것입니다.

## rdt 프로토콜의 송수신
>rdt 프로토콜의 송수신
![](https://velog.velcdn.com/images/97gkswn/post/7ce8db05-6e88-4d61-8431-51b9298db834/image.png)
- 송신 측
-상위계층에서 보내려는 데이터가 있다면 rdt_send()를 호출해 데이터를 rdt프로토콜로 보낸다
-rdt프로토콜에서 하위계층으로 보낼 때 udt_send()를 호출한다
- 수신 측
-하위계층에서 패킷을 받으면 rdt_rcv()를 호출해 rdt프로토콜로 보낸다
-rdt프로토콜에서 상위계층으로 보낼 때 deliver_data()를 호출한다

## rdt 1.0
><rdt1.0  완벽할 때 = 하위 채널이 완전히 신뢰적인 간단한 경우>
![](https://velog.velcdn.com/images/97gkswn/post/2e7ecb58-3205-4049-8636-1de1ce237e54/image.png)
- 송신
그냥 윗 계층에서 보낼거 있다 하고 rdt_send부르면 받아서 패킷만들고 udt_send()로 보냄
- 수신
그냥 패킷 오면 extract하고 윗 계층으로 deliver함

## rdt 2.0
하지만 이건 그냥 이상적인 이야기입니다.
bit error가 있을 때는 아래와 같은 방법을 이용합니다.


>rdt2.0 ( ACK 와 NAK 추가)
![](https://velog.velcdn.com/images/97gkswn/post/ee6aa7da-d020-497b-9139-2222d25d28c6/image.png)
에러를 체크할 때 필요한 checksum 이 추가되었고 잘받았다는 신호인 ACK와 잘못받았다는 신호인 NAK이 추가 되었습니다.
- 송신
패킷에 checksum을 넣어서 전송
상대방으로 부터 ACK or NAK을 기다림
NAK를 받으면 udt_send(sndpkt)로 재전송해줌
ACK를 받으면 wait for call from above 상태로 감
- 수신
패킷이 왔고 에러가 없으면 extract하고 윗계층으로 deliver하고 udt_send(ACK)로 송신자한테 잘받았다고 전해줌
패킷에 에러가 있다면 NAK을 보내줌

## rdt 2.1
근데 rdt2.0도 심각한 문제가 있는데 그것은 애초에 ACK 과 NAK이 상대방에게 전달이 안될 수도 있다는것 입니다. 똑같은 것을 또 받아서

>rdt2.1 ( sequence라는 번호를 도입해 패킷에 순서를 매김)
<br>
- 송신 측
![](https://velog.velcdn.com/images/97gkswn/post/5f97a9cf-9548-4211-8e40-cf6e4aa8e986/image.png)
0번 패킷을 보낸 후 ACK0 나 NAK0를 기다리고 NAK 0을 받으면 재전송하고 ACK1을 받으면 1번 패킷의 call을 기다리고 call하면 1번 패킷을 보냄
그리고 다시 ACK1 이나 NAK1을 기다림
<br>
- 수신 측
![](https://velog.velcdn.com/images/97gkswn/post/3c79a6fa-1b0e-41ec-b5e2-a9fba2fef0db/image.png)
1번 패킷에 오류가 있다면 NAK 1 신호를 보내고 1번 패킷이 오길 기다림
1번 패킷에 오류가 없다면 ACK 1 신호를 보내고 0번 패킷이 오길 기다림


- ack하고 nak을 받는 부분을 계속 확인하는 부분도 있어야하고

- 방금 0을 받았는지 1을 받았는지도 계속 확인해야합니다.

- receiver부분에서도 패킷이 동일한 패킷인지 아닌지 체크해야하고 ack/nak을 잘 보냈는지 확인할 수 없는 단점이 있습니다.
  <br>
  <Br>
    
## rdt 2.2
>rdt 2.2 (NAK을 사용하지 않고 ACK0, ACK1을 사용함)
  -> 패킷이 중복되었을 경우 처리하기 위한 순서번호
![](https://velog.velcdn.com/images/97gkswn/post/64878e6d-7a27-4f99-95e2-6e1d27556b77/image.png)
   - 송신 측
초기 상태는 0을 기다리는 상태
데이터를 보내는 이벤트가 일어나고 시퀀스 0을 넣어서 패킷 전송
그 다음 0 이 있는 ack가 오기를 기다림
만약 ACK1을 받으면 NAK으로 간주
    <BR>
 - 수신 측
      도착한 패킷이 시퀀스도 일치하고 에러가 없다면 application layer에 올리고 network layer에서 ack1을 보냄
시퀀스가 0인 패킷을 받아야 하는데 1인 패킷을 받으면 ack1을 보냄
      
## rdt 3.0
하위 채널이 패킷을 손실하는 경우를 생각해봅시다.
      
송신자는 데이터 패킷이 손실되었는지, ACK가 손실되었는지, 패킷 또는 ACK가 단순히 지나치게 지연된 것인지를 알지 못합니다.

이 때 만약 송신자가 패킷을 잃어버렸다고 확신할 정도로 충분한 시간을 기다릴 수만 있다면, 데이터 패킷은 간단하게 재전송될 수 있습니다.
      
이를 위해 카운트다운 타이머(countdown timer)가 추가됩니다

> 
rdt 3.0 송신자 `카운트 다운 타이머` 추가
      ![](https://velog.velcdn.com/images/97gkswn/post/ac4c8c57-fe19-45a9-9366-d5f92e9ba7b7/image.png)
- 송신
 1. 매 패킷( 첫 번째 또는 재전송 패킷)이 송신된 시간에 타이머를 시작함
 2. 타이머 인터럽트에 반응함(적당한 행동을 취함)
 3. 타이머를 멈춤
      
## pipelined protocol
rdt 3.0은 기능적으로 정확한 프로토콜입니다.
그러나 전송 후 대기(stop-and-wait)방식을 사용하고 있으므로 속도문제가 존재합니다.
>![](https://velog.velcdn.com/images/97gkswn/post/727f3282-ba45-41c3-912b-e45204e5903a/image.png)
위와 같이 확인응답을 기다리느라 시간지연이 발생하죠


>그래서 위의 사진과 같이 확인 응답인 ACK을 기다리지 않고 여러 개의 패킷을 동시에 보내는 방법을 생각합니다.
확인 응답을 기다리기 전에 송신자가 3개의 패킷을 보내면 송신자의 이용률은 3배가 늘죠!
이 기술을 `파이프라이닝(pipelining)`이라고 부릅니다.
      
이 `파이프라이닝`의 오류 회복에는 두 가지가 있습니다.
      
### Go back N (파이프라이닝 첫 번째 오류회복방법)
![](https://velog.velcdn.com/images/97gkswn/post/92c180ff-82ba-494a-a58b-6529de592edc/image.png)

>잘 받은 패킷까지 ACK를 보냅니다.
즉, 위와 같이 중간에 3번 패킷이 손실되면 2번까지 잘받았으므로 ACK2만 보냅니다. 
      
### 단점      
하지만 이는 중간의 패킷하나의 오류 때문에 그 이후 순번의 패킷까지 불필요하게 재전송 해야하는 상황이 발생합니다.       
      
### Selective Repeat (파이프라이닝 두 번째 오류회복방법)
![](https://velog.velcdn.com/images/97gkswn/post/4cd3ffa8-6e6b-45ca-b14e-0dbf0644f5a2/image.png)
      
>선택적 반복이라는 의미로 오류가 발생했다고 판단하는 패킷만 재전송합니다. 
따라서 잘 받은것들만 ACK를 보냅니다.
위와 같이 중간에 3번 패킷이 손실되면 ACK3빼고 ACK 1,2,4,5를 보냅니다.
      
## 정리
이번에는 데이터를 신뢰적으로 제공하기 위한 프로토콜들을 살펴봤습니다.
데이터를 잘 주고받았는지 ACK,NAK을 추가해서 확인하고
sequence라는 순번을 붙여 패킷을 재전송할 때 패킷의 중복을 방지하고
카운트다운 타이머를 추가해 일정 시간동안 아무 응답이 없으면 재전송하고
패킷을 한 개씩 보내는 것이 아니라 한꺼번에 여러 개를 보내고
그렇게 할 때 오류회복을 Go Back N 이나 SR로 오류를 회복하고
이런 전체적인 흐름을 봤습니다!

## 참고
https://code-lab1.tistory.com/26
https://snnchallenge.tistory.com/129   
