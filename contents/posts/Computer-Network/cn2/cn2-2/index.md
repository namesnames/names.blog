---
title: "5계층 [어플리케이션계층] part2"
description: "5계층 [애플리케이션 계층] part2"
date: 2023-07-28
update: 2023-07-28
tags:
  - 컴퓨터네트워크

series: "컴퓨터네트워크"
---

인터넷은 애플리케이션에게 2개의 전송 프로토콜을 제공합니다.
- 무슨말이냐면 애플리케이션에서 메시지를 만들면 그것을 패킷의 형태로 전송을 할건데 이 때 우리의 인터넷은 2개의 방법으로 전송할 수 있게끔 해준다는 말입니다.

그 2개의 방법은 TCP와 UDP입니다.
- TCP (Transmission Control Protocol)
- UDP (User Datagram Protocol)

## TCP vs UDP
![](https://velog.velcdn.com/images/97gkswn/post/0c173ae2-fc70-4223-a007-1da4fd212cb2/image.png)

### TCP
1. 애플리케이션 메시지를 전송하기 전에 클라이언트와 서버가 패킷이 곧 갈테니까 준비해! 라는 식의 정보를 교환함 (핸드쉐이킹 과정)
2. Reliable transport -> 비트가 바뀌거나 data loss를 허용하지 않는 프로토콜임 (신뢰할 수 있음)
3. Flow control(흐름제어) -> 수신측이 데이터 처리가 느려서 송신측에서 보낸 data가 손실 될 수 있어서 수신측의 buffer의 남은양을 송신측에게 알려서 송신측의 window size를 조절하게끔 함 (송신측 window size= 수신측 buffer의 남은양 이 되도록 제어를 함)
4. Congestion control(혼잡제어) -> 한 라우터에 데이터가 몰리게 되면 자신에게 온 데이터를 처리할 수가 없게된다. 따라서  실제 communication 전에 즉 사전에, 수신측에서 받을 수 있는 최대 segment를 정의해서 송신측에게 알려줌

### UDP
1. unreliable data transport -> 데이터 손실이나 순서맞추는것을 신경쓰지 않고 그냥 보내는 프로토콜 (신뢰할 수 없음)
2. TCP와 반대임(flow control,congestion control 뭐 이런거 안함)
<br>
<br>

## 웹과 HTTP
- 웹의 애플리케이션 계층 프로토콜인 HTTP는 웹의 중심입니다.
- HTTP는 hypertext transfer protocol의 약자입니다.
- 웹 페이지는 객체들로 구성이 되는데 대부분의 웹 페이지는 기본 HTML파일과 여러 객체들로 구성이 됩니다. 
- 예를 들어 웹 페이지가 HTML 텍스트와 3개의 PNG파일이 있다고 하면 이 웹 페이지는 4개의 객체를 가지고 있는 셈이 되는거에요.

### 웹 페이지를 불러올 때 일어나는 과정을 볼까?
사용자가 웹 페이지를 요청하면 브라우저는 페이지 내부의 객체에 대한 HTTP요청 메시지를 서버에 보냅니다.
`이 때 HTTP는 TCP를 전송 프로토콜로 사용합니다`

1. HTTP 클라이언트는 먼저 서버에 TCP 연결을 시작합니다.

2. 일단 연결이 이루어지면, 브라우저와 서버 프로세스는 그들의 소켓 인터페이스를 통해 TCP로 접속합니다.
(클라이언트 측에서 보면 소켓은 서버 프로세스와 TCP연결 사이의 출입구인 셈임)

3. 클라이언트는 HTTP 요청 메시지를 소켓으로 보내고 / 소켓으로부터 HTTP응답 메시지를 받습니다. 
(일단 클라이언트가 메시지를 소켓으로 보내면 메시지는 클라이언트의 손을 떠난 것이고 TCP의 손에 쥐어지게 됨 -> TCP의 특성 중 확실하게 메시지를 전달해주는 특성을 기억해보면 클라이언트가 일단 메시지를 보내면 서버에 잘 도착한다는 것을 의미함)

4. 서버도 마찬가지로 소켓으로부터 메시지를 받고 소켓에 메시지를 보냄 

여기서 이제 계층구조의 장점이 나타나는데 애플리케이션계층 입장에서는 메시지를 일단 보내면 그 이후의 과정들은 신경을 쓰지 않아도 돼요! 그래서 이전에 내가 뭘 보냈었는지 전혀 기억하지 않아도 된답니다. 그래서 HTTP를 비상태 프로토콜(stateless protocol)이라고 해요!



>  < HTTP 특성 > 
- 1. TCP를 이용한다
->클라이언트가 서버에게 TCP connection을 요청하면 서버가 수락하고 HTTP 메시지를 주고 받는다
주고 받으면 TCP connection은 closed된다
- 2. HTTP는 stateless다 
->내가 과거에 뭐뭐했다 라는것을 저장하지않음

## HTTP 도 두 개로 나뉩니다
### non-persistent HTTP (비지속 연결)
뭐 하나 주고 받을때마다 계속 TCP connection을 맺어야합니다! 


### persistent HTTP (지속 연결)
최초에 한 번만 TCP connection을 맺으면 여러개의 객체들을 계속 주고받을 수 있음

>두 개로 나뉘긴 하지만 지속 연결이 디폴트고 비지속연결은 설정할 수 있는 옵션입니다!

### 예제1
자세히 보면 non-persistent에는 parallel쓰냐 안쓰냐 2개로 나뉘고
persistent에는 windowing을 쓰냐 안쓰냐 2개로 또 나뉜다
그래서 아래와 같이 4개의 case가 존재
![](https://velog.velcdn.com/images/97gkswn/post/be953ea8-9c0c-4550-b6b0-cef2e3dfbfea/image.png)
1. non-persis + no parallel
2. non-persis + yes parallel
3. persis + no windowing
4. persis + yes windowing

### 예제2
![](https://velog.velcdn.com/images/97gkswn/post/dec295c2-ba6e-4379-99be-e0393548772f/image.png)
이 문제는 1. non-persis + parallel 과 2. persis + no windowing만 다뤘다

1. non-persis + parallel
![](https://velog.velcdn.com/images/97gkswn/post/71f13faf-fff8-45a1-8520-7d31fd743c12/image.png)

2. persis + no windowing
![](https://velog.velcdn.com/images/97gkswn/post/2108e6da-6c3a-4415-9244-d579c659d555/image.png)

## HTTP 메시지 포맷
### HTTP 요청 메시지
#### 예시
>![](https://velog.velcdn.com/images/97gkswn/post/fb3345c3-2571-48f7-87c0-06120e7866cf/image.png)
먼저 예시를 보자
- 첫 줄은 `요청 라인`이라고 부르고, 이후의 줄들을 `헤더 라인`이라고 부름
- 첫 줄인 요청 라인은 방식(method) / URL / HTTL버전을 의미한다
`즉, GET방식이고 somedir/page.html객체를 요청하고있고 HTTP버전은 1.1`
- HOST : www.someschool.edu 
-> 객체가 존재하는 호스트를 명시함
🧐 근데 호스트까지 TCP연결이 맺어진 거면 이게 필요없는거 아닌가요??
💡 저거는 나 중에 웹 프록시 캐시에서 필요로 한답니다!
- Connection : close
-> 서버가 요청한 객체를 보내주면 연결을 닫기를 원한다는 것을 의미
- User-agent : Mozilla/5.0
-> 사용자 에이전트를 의미 = 서버에게 요청하는 브라우저 타입을 의미
-> 즉, 사용자가 Mozilla/5.0,파이어 폭스 브라우저로 보낸 것임
- Accept-language : fr
-> 사용자가 객체의 프랑스어 버전 (서버에 존재한다면)을 원하고 있음을 의미

#### HTTP 요청 메시지의 일반 포맷
>![](https://velog.velcdn.com/images/97gkswn/post/874370e8-2057-4b60-810e-2154ba1f8970/image.png)
- 특징을 보면 먼저 `헤더 라인` 뒤에 `개체 몸체`가 있다.
- `개체 몸체`는 GET방식에서는 비어있고 POST방식에서 사용이 된다.

<br>
<br>


### HTTP 응답 메시지
#### 예시

>![](https://velog.velcdn.com/images/97gkswn/post/2cc55945-8352-46b1-a6b1-7ae2209b3da1/image.png)
- 보면 맨 위에부터 `상태 라인(status line)`, `6개의 헤더 라인`,`개체 몸체(데이터 들)` 로 이루어져 있다. `개체 몸체`는 사용자가 요청한 객체들입니다.
- HTTP/ 1.1 200 OK
-> 서버가 HTTP / 1.1을 사용하고 있고, 모든 것이 양호함(OK)을 의미 
-> 즉, 서버가 요청한 객체들을 찾아서 보냈다는 말입니다.
- Connection :Close
-> 클라이언트에게 메시지를 보낸 후 TCP 연결을 닫겠다는 말입니다.
- Date : Tue, 18 Aug 2015 15:44:04 GMT
-> 서버에 의해 생성되고 보낸 날짜와 시간을 의미
- Server : Apache/2.2.3 
-> 응답 메시지가 아파치 웹 서버에 의해 만들어졌다는 것을 의미합니다.
- Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT
-> 객체가 생성되거나 마지막으로 수정된 날짜와 시간을 의미합니다.
- Content-Length: 6821
-> 보내는 객체의 바이트 수를 의미합니다.
- Content-Type : text/html
-> 개체 몸체 내부의 객체가 HTML텍스트임을 나타냅니다.

#### HTTP응답 메시지의 일반 포멧
>![](https://velog.velcdn.com/images/97gkswn/post/4b1d502d-a23d-42d6-a202-ea724c4a1faa/image.png)
- 앞서 봤던 HTTP요청 메시지의 일반 포멧과 상당히 유사합니다.
- 맨 위의 `상태 라인`이 약간 다른데 아래와 같이 `대표적인 상태 코드`가 있습니다.
---
- 200 OK: 요청이 성공했고,정보가 응답으로 보내졌다.
- 301 Moved Permanently:요청 객체가 영원히 이동되었다. 새로운URL은
응답메시지의 Location: 헤더에나와있다. 클라이언트소프트웨어는자동으로 이 새로운URL 을 추출한다.
-  400 Bad Request: 서버가 요청을 이해할 수 없다는 일반 오류코드다.
-  404 Not Found: 요청 문서가 서버에 존재하지 않는다.
-  505 HTTP Version Not Supported: 요청 HTTP 프로토콜 버전을 서버가 지원하지 않는다.


## 다음으로
다음에는 쿠키와 캐싱에 대해서 정리해보겠습니다!


## 참고
https://dkswnkk.tistory.com/496
https://occamsrazr.net/tt/3
ChatGPT-3.5
