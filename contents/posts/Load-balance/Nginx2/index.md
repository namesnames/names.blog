---
title: "Nginx를 이용한 로드밸런싱 테스트 (로드밸런싱 알고리즘)"
description: "Nginx를 이용한 로드밸런싱 테스트 (로드밸런싱 알고리즘)"
date: 2023-08-29
update: 2023-08-29
tags:
  - Nginx
  - 로드밸런싱
  - k6

---

## 개요
지난 번에 [Nginx를 이용해 로드밸런스 서버를 만들어 트래픽을 분산시키기](https://velog.io/@97gkswn/Nginx%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%B4-%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%8A%A4-%EC%84%9C%EB%B2%84%EB%A5%BC-%EB%A7%8C%EB%93%A4%EC%96%B4-%ED%8A%B8%EB%9E%98%ED%94%BD%EC%9D%84-%EB%B6%84%EC%82%B0%EC%8B%9C%ED%82%A4%EA%B8%B0)에서 로드밸런스 서버 1대와 어플리케이션 서버 2대를 설정해서 Lionz프로젝트에 로드밸런싱을 적용해봤습니다.
점차 부하를 늘려보며 어디까지 버틸 수 있을까 테스트를 해보려고 합니다.

>- 600명 (에러x)
![](https://velog.velcdn.com/images/97gkswn/post/4d118b7c-dc3f-4610-a97c-3fa23fbb854c/image.png)
<br>
- 1000명 (에러O)
![](https://velog.velcdn.com/images/97gkswn/post/2547ce7f-1e31-4d1c-b905-856f7902277f/image.png)
<br>
- Grafana 모니터링
![](https://velog.velcdn.com/images/97gkswn/post/280bcd97-b139-439d-a5b3-4af58c33ba86/image.png)
그래프를 보면 600명(vus=600)으로 보냈을 때 두 서버의 CPU이용률이 50%정도를 보였고 
1000명(vus=1000)으로 보냈을 때 두 서버의 CPU이용률이 약 70%정도를 보이면서 에러가 발생했습니다.


## 서버를 한 대 더 늘려볼까?
### 생각하는 그림
![](https://velog.velcdn.com/images/97gkswn/post/22ea1ef9-8dab-42f1-ab90-f98ac6de72a9/image.png)

### 네이버클라우드 서버 추가 생성
![](https://velog.velcdn.com/images/97gkswn/post/bdb8f86d-b48a-47d1-b2fe-699d60e7d3cc/image.png)
lionz-4라는 이름으로 서버를 한 대 추가적으로 생성했습니다.
이어서 바로 [내도메인.한국](https://xn--220b31d95hq8o.xn--3e0b707e/)에서 도메인을 연결해줬습니다.

### Nginx (로드밸런스 서버) 수정
```
cd /etc/nginx/sites-enabled
```
을 입력해 sites-enabled폴더로 이동하고 그 안에 만들어놨던 myapp이라는 파일을 수정했습니다.
```
upstream 원하는이름 {
  //원하는 알고리즘
  server 111.111.111.111:8080;
  server 222.222.222.222:8080;
  server 333.333.333.333:8080;
}
server {
  listen 80;  
  location / {
    proxy_pass http://원하는이름;
  }
}
```
그냥 `server 새로만든서버 IP:8080;`만 추가해주면 됩니다!

### 프로메테우스 yml수정
```
vi $(brew --prefix)/etc/prometheus.yml
```
를 입력해 prometheus.yml로 이동합니다.
>![](https://velog.velcdn.com/images/97gkswn/post/bea0562b-7365-452c-8f40-c9be886805f2/image.png)
target부분에 새로 만든 서버의 도메인인 hanzu.n-e.kr를 추가해줍니다.

그리고 재시작해줍니다.

![](https://velog.velcdn.com/images/97gkswn/post/31d29a15-78fc-4417-8164-c29629fb7fab/image.png)
localhost:9090을 들어가 확인해보니 아주 잘 된 것 같습니다!

### Grafana 확인
![](https://velog.velcdn.com/images/97gkswn/post/ad77ef21-447d-4172-b4c9-4a8e9de37012/image.png)
Grafana도 들어가보니 제대로 추가가 된 것 같습니다!

## 다시 1000명 보내보자
```
k6 run --vus 1000 --duration 30s scenario.ts
```
![](https://velog.velcdn.com/images/97gkswn/post/69666959-8bc1-4a8d-8f25-4dd8e0cb6133/image.png)
오.. 1000명으로 설정하고 보냈는데 처음으로 에러가 안뜨고 마무리 되었습니다.
k6로 저렇게 명령어를 입력하면 test.csv라는 파일이 만들어져서 하나하나 요청들의 상태들 확인할 수 있는데 전부 `True`로 정상적으로 보내진 것을 확인했습니다.

Grafana로 한 번 볼까요?
![](https://velog.velcdn.com/images/97gkswn/post/63ee4405-4eb0-4398-ac59-5a3b28da80bc/image.png)
보시면 서버 한 대가 좀 많이 무리를 하긴 했지만 그래도 나머지 두 서버도 열심히 일한 것 같습니다. 그러니까 에러가 안났겠죠?

## 로드밸런싱 알고리즘
방금 부하를 줬을 때 서버들이 견디긴 했지만 서버 한 대가 많이 무리를 했어서 약간 신경이 쓰입니다.
여러 방법들을 적용해서 관찰해보겠습니다.

>- 로드밸런싱 알고리즘이란?
-> 로드밸런스 서버가 애플리케이션 서버들에게 트래픽을 어떻게 분배할지를 결정해야하는데 그 때 결정할 수 있는 알고리즘들이 여러 개 존재합니다.

### 라운드 로빈 (Round Robin)
>- 모든 서버에 순서대로 요청을 분배하는 방식입니다.
- 간단하고 균등하게 트래픽을 분산합니다.
- 서버의 상태에 관계없이 동작하기 때문에 서버들의 리소스나 성능 차이를 고려하지 않습니다.

```
upstream 원하는 이름 {
  server 111.111.111.111:8080;
  server 222.222.222.222:8080;
  server 333.333.333.333:8080;
}
```
위와 같이 아무것도 설정하지 않으면 기본으로 라운드 로빈으로 실행이됩니다.
아까 위에서 봤던 결과가 라운드 로빈일 때 입니다.
> 결과 (라운드 로빈)
![](https://velog.velcdn.com/images/97gkswn/post/63ee4405-4eb0-4398-ac59-5a3b28da80bc/image.png)

### 최소 연결 수 (Least Connections)

>- 현재 연결 수가 가장 적은 서버로 트래픽을 보내는 방식입니다.
- 서버들의 리소스 사용량을 고려하여 트래픽을 분산합니다.
- 서버들의 연결 수가 다르게 분포되어 있을 때 효과적입니다.

```
upstream 원하는 이름 {

  least_conn; // Least Connections 알고리즘
  server 111.111.111.111:8080;
  server 222.222.222.222:8080;
  server 333.333.333.333:8080;
}
```
>- 부하
![](https://velog.velcdn.com/images/97gkswn/post/dfdcfb41-3c1c-4c9c-b9a9-992d9de3da83/image.png)
<br>
- 결과 (최소 연결 수)
![](https://velog.velcdn.com/images/97gkswn/post/0c3da082-80d2-42ef-8cf0-6c2914e918f0/image.png)
똑같이 1000명으로 설정해서 보낸건데 CPU사용률이 제일 높은게 25%밖에 안되고 되게 균형있게 부하를 부담한 것 같은 느낌입니다.

### 랜덤 (Random)
>트래픽을 무작위로 분배하는 알고리즘입니다.


```
upstream 원하는 이름 {

  random; // Random 알고리즘
  server 111.111.111.111:8080;
  server 222.222.222.222:8080;
  server 333.333.333.333:8080;
}
```
>- 부하
![](https://velog.velcdn.com/images/97gkswn/post/8d59c74a-3cb0-46cf-8998-ac71952cc649/image.png)
역시 전송은 계속 잘 되고있습니다.
<br>
- 결과 (Random)
![](https://velog.velcdn.com/images/97gkswn/post/ce98867b-6136-4aeb-9ab8-d3ff1f52e6b0/image.png)
세 대의 서버가 정말 균등하게 트래픽을 분산받고 있는 모습입니다.
제가 볼 땐 현재까지는 Random이 가장 이상적인 것 같지만 Random알고리즘은 서버의 상태를 고려하지 않고 진행하기 때문에 실제로 적용하기에는 무리가 있어보인다는 생각입니다!

## 결론
아마 실제로 제가 많은 사람들이 이용하는 서비스를 만들게 된다면 로드밸런싱 알고리즘은 Least Connections (최소 연결 수)로 할 것 같습니다.
부하를 적게 받고 있는 서버에 트래픽을 보내기 때문에 꽤나 이상적으로 트래픽이 분산이 되고 서버의 상태를 고려하기 때문에 서버가 망가지면 트래픽을 보내지 않기 때문에 안전할 것 같다는 생각을 하게 됐습니다.

