---
title: "컴퓨터 네트워크 Ch2"
description: "컴퓨터 네트워크 Ch2"
date: 2022-10-16
update: 2022-10-16
tags:
  - 컴퓨터네트워크

series: "컴퓨터네트워크"
---

# 1.server & client
![](https://velog.velcdn.com/images/97gkswn/post/ea20593d-8c88-4409-b787-51a9222231b8/image.png)
서버: 서버는 항상 켜져 있고 주소는 영구적이어야 한다 
클라이언트: 간헐적으로 서버와 연결되며 영구적인 주소가 필요가없다

# 2. TCP vs UDP
![](https://velog.velcdn.com/images/97gkswn/post/0c173ae2-fc70-4223-a007-1da4fd212cb2/image.png)
## TCP
1. reliable transport -> 비트가 바뀌거나 data loss를 허용하지 않는다 (신뢰할 수 있음)
2. flow control(흐름제어) -> 수신측이 데이터 처리가 느려서 송신측에서 보낸 data가 손실 될 수 있어서 수신측의 buffer의 남은양을 송신측에게 알려서 송신측의 window size를 조절하게 한다(송신측window size= 수신측buffer의 남은양 이 되도록)
3. congestion control(혼잡제어) -> 한 라우터에 데이터가 몰리게 되면 자신에게 온 데이터를 처리할 수가 없게된다. 따라서  실제 communication 전에 즉 사전에, 수신측에서 받을 수 있는 최대 segment를 정의해서 송신측에게 알려줌
## UDP
1. unreliable data transport -> 데이터 손실이나 순서맞추는것을 신경쓰지 않고 그냥 보낸다 (신뢰할 수 없음)
2. TCP와 반대임(flow control,congestion control 뭐 이런거 안함)
<br>
<br>

# HTTP
## HTTP overview
hypertext transfer protocol의 약자
- 1. TCP를 이용한다
->클라이언트가 서버에게 TCP connection을 요청하면 서버가 수락하고 HTTP 메시지를 주고 받는다
주고 받으면 TCP connection은 closed된다
- 2. HTTP는 stateless다 
->내가 과거에 뭐뭐했다 라는것을 저장하지않음

HTTP connections은 크게 두개로 나뉜다
## non-persistent HTTP
뭐 하나 주고 받을때마다 계속 TCP connection을 맺어야함


## persistent HTTP
최초에 한 번만 TCP connection을 맺으면 여러개의 objects들을 계속 주고받을 수 있음

## 예제1
자세히 보면 non-persistent에는 parallel쓰냐 안쓰냐 2개로 나뉘고
persistent에는 windowing을 쓰냐 안쓰냐 2개로 또 나뉜다
그래서 아래와 같이 4개의 case가 존재
![](https://velog.velcdn.com/images/97gkswn/post/be953ea8-9c0c-4550-b6b0-cef2e3dfbfea/image.png)
1. non-persis + no parallel
2. non-persis + yes parallel
3. persis + no windowing
4. persis + yes windowing

## 예제2
![](https://velog.velcdn.com/images/97gkswn/post/dec295c2-ba6e-4379-99be-e0393548772f/image.png)
이 문제는 1. non-persis + parallel 과 2. persis + no windowing만 다뤘다

1. non-persis + parallel
![](https://velog.velcdn.com/images/97gkswn/post/71f13faf-fff8-45a1-8520-7d31fd743c12/image.png)

2. persis + no windowing
![](https://velog.velcdn.com/images/97gkswn/post/2108e6da-6c3a-4415-9244-d579c659d555/image.png)

# cookies
http는 기본적으로 과거의 행적(history)을 기억하지 않는 stateless한 특성을 가지고 있는데 cookie가 stateful 하게끔 해준다
![](https://velog.velcdn.com/images/97gkswn/post/95744817-66b9-40d1-b0dd-87674f0e408f/image.png)
각 사용자에게  위와 같이 헤더에 실어서 cookie를 부여함
사용자는 이제부터 헤더에 그 부여된 cookie를 서버에 보내면 서버가 알아차리고 기억함
# web caches (proxy server)
서버와 클라이언트의 거리가 굉장히 멀면 그 중간에 서버를 하나 더 두는것이 합리적이다 = proxy server 
자주 사용하는 objects는 proxy server에 두고 가끔 사용하는 objects는 original server에 가서 가져오도록 한다

