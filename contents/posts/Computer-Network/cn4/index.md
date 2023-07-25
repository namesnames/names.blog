---
title: "컴퓨터 네트워크 CH4"
description: "컴퓨터 네트워크 CH4"
date: 2022-12-01
update: 2022-12-01
tags:
  - 컴퓨터네트워크

series: "컴퓨터네트워크"
---

## DHCP (Dynamic Host Configuration Protocol)
IP주소 얻을 때의 프로토콜
현재 컴퓨터를 사용하는 사람에게 IP를 주고 
안쓰고 있으면 뺐어서 다른 사람에게 IP주고 이런식으로 동적으로 사용하게함

>![](https://velog.velcdn.com/images/97gkswn/post/e7874c7e-576b-45f0-b50d-c07f1553ac4e/image.png)
![](https://velog.velcdn.com/images/97gkswn/post/017dbdd7-8a23-463c-8258-06c991fef0e9/image.png)
discover
offer
request
ack 
의 과정이 있음

## HOL blocking
>![](https://velog.velcdn.com/images/97gkswn/post/ca14986c-7381-45ef-903b-283b9c7fb0f7/image.png)
input buffer에서 queue의 앞에 있는 녀석이 자기 차례 기다리느라 뒤에있는 친구가 못감 -> delay 발생

## 네트워크 (패킷 교환) 알고리즘
### link state routing algorithm
자신이 목적지까지의 향하는데의 모든 node들의 상태를 다 알고있음 
(내가 몇번 노드와 연결되어있고 cost는 몇이 드는지)

장점:  Convergence Time 이 짧음( Convergence Time : 라우팅 테이블에 생겨난 변화를 모든 라우터가 알 때까지 걸리는 시간 )
단잠: 메모리 낭비 (모든 node의 정보를 알고있으므로)

ex) 다익스트라(Dijkstra)

### distance vector routing algorithm
"내가 어디로 갈 수 있어, 몇 cost로" 의 내용을 주변에만 뿌림
link state 와 다르게 모든 node의 정보를 알지못함

장점 : 메모리가 절약됨
단점 : Convergence Time이 느리다. ( Convergence Time : 라우팅 테이블에 생겨난 변화를 모든 라우터가 알 때까지 걸리는 시간 )

ex) Bellman-ford