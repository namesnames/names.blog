---
title: "컴퓨터 네트워크 CH3 트랜스포트"
description: "컴퓨터 네트워크 CH3 트랜스포트"
date: 2022-10-12
update: 2022-10-12
tags:
  - 컴퓨터네트워크

series: "컴퓨터네트워크"
---

3장
트랜스포트 계층
end system 말단에서 일어난다 (라우터들이 아닌 host부분)
application 계층에서 네트워크 계층까지 전달하는 역할
보내는쪽: app 메시지를 segments로 쪼개 네트워크 계층으로 보냄
받는쪽: segments들을 재조립해 app 계층으로 보냄
결국 
네트워크 계층은 host간의 통신이고
트랜스포트 계층은 프로세스간의 통신임
앤과 빌의 예시
서부와 동부에 집이 하나씩 있다고 생각하자
각 집에는 12명의 아이들이 있고 서부와 동부의 아이들은 서로 사촌지간의 관계라 편지를 많이 주고받음
12명중에서 한명이 모든 편지를 모아 우편배달부한테 전달해주는데 그 역할을
서부에는 앤, 동부에는 빌이 하고있다
여기서 역할들을 비유해보자면
애플리케이션 메시지=봉투 안의 편지
프로세스 = 사촌형제
호스트(host or end system) = 집
트랜스포츠 계층 프로토콜 = 앤 과 빌
네트워크 계층 프로토콜 = 우편서비스 (우편 집배원을 포함한)
3.2 mutiplexing and demultiplexing 다중화와 비다중화
간단히 말해서 
앤과 빌이 다른 11명의 아이들에게 편지를 받아서 모아서 우편 집배원에게 주는 것을 다중화라고 하고
또 우편 집배원한테 편지들을 받아서 이것들을 각 11명의 아이들에게 나눠주는 것을 역다중화라고 함

트랜스포트 계층은 애플리케이션으로부터 메시지를 받아서 이것들을 조각내어 segments들로 만들고 여기에 헤더를 붙이고 
그런 segments들을 네트워크 계층으로 캡슐화 하여 전달함 
이렇게 캡슐화하여 네트워크 계층으로 전달하는 것을 다중화 라고함
이렇게 해서 네트워크 계층이 상대 호스트에게 보내고 상대 호스트쪽의 트랜스포트 계층은 segments들의 포트번호와 헤더를 보고 적절한 소켓에 전달해줌
이때 소켓은 애플리케이션 계층과 트랜스포터 계층사이에 메시지를 주고받는 통로임

소켓에는 TCP소켓이랑 UDP소켓이 있는데
TCP 소켓은 
source IP주소
source port number
dest IP 주소
dest port number 이 4개로 식별한대
여러 개의 segments들을 받을 때 이 4개가 일치하지 않으면 목적지가 같아도 서로 다른 소켓(통로)을 사용하는듯

네트워크 계층에서의 프로토콜을 인터네프로토콜 이라고 하고 줄여서 IP라고 한다
IP는 호스트간의 데이터 전달을 위해 최대한 노력을 하지만 데이터가 잘 전달됐는지, 순서대로 잘 갔는지 등의 책임은 지지 않는다 (보장을 안해줌)
그래서 IP를 비신뢰적인 서비스라고 함



RDT(rdt)
트랜스포트 계층에서는 신뢰성 있는 데이터교환을 하고싶지만 하위계층에서는 신뢰성을 보장할 수 없기 때문에 트랜스포트 계층에서는 RDT프로토콜을 이용한다


>rdt 프로토콜의 송수신
![](https://velog.velcdn.com/images/97gkswn/post/7ce8db05-6e88-4d61-8431-51b9298db834/image.png)
- 송신 측
-상위계층에서 보내려는 데이터가 있다면 rdt_send()를 호출해 데이터를 rdt프로토콜로 보낸다
-rdt프로토콜에서 하위계층으로 보낼 때 udt_send()를 호출한다
- 수신 측
-하위계층에서 패킷을 받으면 rdt_rcv()를 호출해 rdt프로토콜로 보낸다
-rdt프로토콜에서 상위계층으로 보낼 때 deliver_data()를 호출한다


>rdt1.0  완벽할 때
![](https://velog.velcdn.com/images/97gkswn/post/2e7ecb58-3205-4049-8636-1de1ce237e54/image.png)
- 송신
그냥 윗 계층에서 보낼거 있다 하고 rdt_send부르면 받아서 패킷만들고 udt_send()로 보냄
- 수신
그냥 패킷 오면 extract하고 윗 계층으로 deliver함

이건 그냥 이상적인 이야기 
bit error가 있을 때는 아래와 같은 방법을 이용한다


>rdt2.0
![](https://velog.velcdn.com/images/97gkswn/post/ee6aa7da-d020-497b-9139-2222d25d28c6/image.png)
에러를 체크할 때 필요한 checksum 이 추가되었고 잘받았다는 신호인 ACK와 잘못받았다는 신호인 NAK이 추가되었다
- 송신
패킷에 checksum을 넣어서 전송
상대방으로 부터 ACK or NAK을 기다림
NAK를 받으면 udt_send(sndpkt)로 재전송해줌
ACK를 받으면 wait for call from above 상태로 감
- 수신
패킷이 왔고 에러가 없으면 extract하고 윗계층으로 deliver하고 udt_send(ACK)로 송신자한테 잘받았다고 전해줌
패킷에 에러가 있다면 NAK을 보내줌


근데 rdt2.0도 심각한 문제가 있는데 애초에 ACK 과 NAK이 상대방에게 전달이 안될 수도 있다는것임

>rdt2.1 ( sequence라는 번호를 도입해 패킷에 순서를 매김)
<br>
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

<Br>
  ack하고 nak을 받는 부분을 계속 확인하는 부분도 있어야하고

방금 0을 받았는지 1을 받았는지도 계속 확인해야함

receiver부분에서도 패킷이 동일한 패킷인지 아닌지 체크해야하고 ack/nak을 잘 보냈는지 확인할 수 없는 단점이 있음
  <br>
  <Br>
    

>rdt 2.2 (NAK을 사용하지 않고 ACK0,ACK1을 사용함)
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
      
참고:
https://code-lab1.tistory.com/26
https://snnchallenge.tistory.com/129

# pipelined protocol
아래 둘다 ACK없이 N개의 패킷을 보낼 수 있다   
cumulative ack -> ACK2를 보내면 2번까지는 잘 받았다는 의미
      
## Go back N
![](https://velog.velcdn.com/images/97gkswn/post/92c180ff-82ba-494a-a58b-6529de592edc/image.png)

잘 받은것까지 ACK를 보냄
위와 같이 중간에 3번 패킷이 손실되면 2번까지 잘받았으므로 ACK2만 보냄
      
## Selective Repeat
![](https://velog.velcdn.com/images/97gkswn/post/4cd3ffa8-6e6b-45ca-b14e-0dbf0644f5a2/image.png)
      
잘 받은것들만 ACK를 보냄
위와 같이 중간에 3번 패킷이 손실되면 ACK3빼고 ACK 1,2,4,5를 보냄
      
# Slow start
>![](https://velog.velcdn.com/images/97gkswn/post/9aaf5c0f-2458-4744-8962-f8a625d42407/image.png)
![](https://velog.velcdn.com/images/97gkswn/post/82ed7878-ea29-4025-a908-21b19297ed9a/image.png)

      

      