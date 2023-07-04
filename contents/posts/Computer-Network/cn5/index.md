---
title: "컴퓨터 네트워크 CH5"
description: "컴퓨터 네트워크 CH5"
date: 2022-11-30
update: 2022-11-30
tags:
  - 컴퓨터네트워크

series: "컴퓨터네트워크"
---

# 다중 접속 프로토콜
>![](https://velog.velcdn.com/images/97gkswn/post/c495af91-1d8f-48b3-be75-54a4ad736ff9/image.png)
위에는 3가지 방법의 프로토콜이 소개되어 있다

# (MAC)링크 계층 주소체계와 ARP
링크 계층 주소는 LAN 주소 또는 물리 주소 또는 MAC주소로 알려져있다
>![](https://velog.velcdn.com/images/97gkswn/post/f6fc589e-7465-482c-9d9e-ae03e8a95d8b/image.png)
위와 같이 랜에 연결된 각 어댑터는 유일한 MAC주소를 가진다

MAC주소의 특징은 어떤 어댑터도 동일한 주소를 갖지 않는다는 것이다

MAC: 주민등록증 (어디로 가든지 바뀌지 않음)
IP: 우편주소 (사람이 이사할 때마다 변경됨)
의 느낌으로 생각하면 편하다

## ARP (Address Resolution Protocol)
네트워크 계층 주소(IP)와 링크 계층 주소(MAC) 사이의 변환을 ARP가 해준다
한 host에서 다른 host로 데이터그램을 전송하기 위해서는 출발지host쪽에서 자신의 어댑터에게 목적지host에 해당하는 IP주소와 MAC주소를 줘야한다(IP는 알고있다고 가정) 
->그런데 목적지의 MAC주소는 어떻게 알고 어댑터에게 알려주지?
목적지의 IP주소를 알고있으면 ARP를 통해 그에 맞는 MAC주소로 변환해줌

그래서 각 호스트와 라우터는 ARP테이블을 갖고있다
ARP테이블이란 IP주소와 MAC주소 간의 매핑정보를 포함한다.
또한 각 매핑이 언제 삭제되는지 TTL(Time-To-Live)값을 포함한다.

ARP테이블은 다음과 같이 구성되어 있다
<IP address , MAC address , TTL>

MAC은 링크계층이고
IP는 네트워크계층임
그래서 패킷에 헤더에 MAC주소가 있고
뒤에 datagram IP주소가 들어감

### 유선일 때 ARP
>![](https://velog.velcdn.com/images/97gkswn/post/42a48258-1d09-44d8-8cf7-c244f83ebcfb/image.png)
유선일 때는 ARP패킷에 MAC주소 2개 , IP주소 2개가 위와 같이 들어간다
A가 B의 MAC주소를 알고 싶어서 ARP query를 broadcast하면 라우터까지만 전달이 되고 라우터가 자신의 MAC주소를 알려줘서 "이제 B한테 보내고 싶으면 나한테 보내" 라고 함


### 무선일 때 ARP
![](https://velog.velcdn.com/images/97gkswn/post/2c06f4e5-63c0-485f-851d-a42cf9af8647/image.png)
무선일 때는 MAC주소만 3~4개 사용한다
이 때 목적지의 MAC주소를 모르면 address3에 FFFF를 넣어서 broadcast해서 얻어낸다

