---
title: "컴퓨터 네트워크 Ch1 (문풀포함)"
description: "컴퓨터 네트워크 Ch1 (문풀포함)"
date: 2022-10-16
update: 2022-10-16
tags:
  - 컴퓨터네트워크

series: "컴퓨터네트워크"
---

링크와 스위치의 네트워크를 통해 데이터를 이동시키는 방식에는 패킷교환과 회선교환이라는 두 가지 기본 방식이 있다

# 패킷교환(packet swithcing)
데이터를 여러조각(packet)으로 쪼개 전송하는 방법
아래와 같이 총 전송시간은 4개의 delay의 합으로 결정된다
데이터를 packet으로 쪼개서 보내기 때문에 Max user는 없지만 사용자가 많아지면 data loss가 생길 수 있다
그리고 circuit swithcing보다 빠르다
![](https://velog.velcdn.com/images/97gkswn/post/2df557b3-623b-4fed-b7aa-41404af544fc/image.png)
![](https://velog.velcdn.com/images/97gkswn/post/36c09a27-d1ba-45ad-9909-0dd30b71f0d3/image.png)

- 전송지연은 라우터가 패킷을 내보내는데 걸리는 시간이고
(미끄럼틀을 탈 때 몸을 넣는시간이라고 생각하면 쉬움)
dtrans = L/R (패킷의 길이를 R(링크 대역폭)으로 나눈 값)

- 전파지연은 한 라우터에서 다음라우터로 전파되는데 걸리는 시간이다
(미끄럼틀을 타고 가는 시간)
dprop = d/s (링크의 길이를 propagation speed로 나눈 값)

(첫번째 비트와 마지막 비트 헷갈리지말것)

# 회선교환(circuit switching)
packet network와는 다르게 circuit network는 데이터가 호스트에서 목적지로 가는 경로가 단 하나임 (유일한 경로를 통해 데이터를 전송)

때문에 circuit만 설정하면 데이터는 그저 보내기만 하면 되는데
그러기 위해선 예약을 해야하고 사용되는 회선 전체를 독점(dedicated)함

링크당 대역폭이 있기 때문에 사용자의 수가 제한이되고
데이터를 쪼개보내는 packet switching보다 느리다


# 문제풀이 (문제는 번호만)
## p6
![](https://velog.velcdn.com/images/97gkswn/post/84ecb6c8-5761-43b6-acc3-43d83c5cb4dc/image.png)

![](https://velog.velcdn.com/images/97gkswn/post/3ab92871-3c2c-4862-908b-8f02bb3a4441/image.png)

## p8
![](https://velog.velcdn.com/images/97gkswn/post/cae1afe5-a8d1-4b51-ad8b-d1b2633e9a8b/image.png)


## p10
![](https://velog.velcdn.com/images/97gkswn/post/5059f92b-a66e-407f-898c-39538d680bbb/image.png)


## p11
![](https://velog.velcdn.com/images/97gkswn/post/551b750d-2929-4c20-a7cb-44610f6942c8/image.png)

## p25
![](https://velog.velcdn.com/images/97gkswn/post/88e87221-be06-4c6b-9b8e-b2ba9d4f886e/image.png)
![](https://velog.velcdn.com/images/97gkswn/post/c8777149-239f-4e8e-b595-f7b36be0a4fb/image.png)

## p31
![](https://velog.velcdn.com/images/97gkswn/post/e35335af-8335-46b3-87b0-9172bddd30dd/image.png)
![](https://velog.velcdn.com/images/97gkswn/post/4ef6a353-1fbb-47e8-aab8-74090f1bfc75/image.png)







