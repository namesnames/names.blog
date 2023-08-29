---
title: "Nginx를 이용해 로드밸런스 서버를 만들어 트래픽을 분산시키기"
description: "Nginx를 이용해 로드밸런스 서버를 만들어 트래픽을 분산시키기"
date: 2023-08-28
update: 2023-08-28
tags:
  - Nginx
  - 로드밸런싱
  - k6

---

## 개요
Lionz 프로젝트가 많은 양의 부하를 견디도록 로드밸런스를 설정을 해보려고 합니다.
이전 포스팅에서 https://velog.io/@97gkswn/k6 에서 부하를 줬더니 서버가 감당하지 못하고 에러가 많이 났던 부분을 다뤘습니다.
이번에는 로드밸런스를 적용해 많은 부하를 감당할 수 있게 해보려고 합니다!

기존에 1개의 서버를 배포했을 때는 aws에서 Free Tier서버를 이용해 배포했었습니다.
하지만 이번에 할 로드밸런스는 여러 대의 서버가 필요하기 때문에 요금이 조금 나올 것으로 생각해서 크레딧을 받아서 돈이 좀 여유있는 [네이버 클라우드](https://www.ncloud.com/)에서 진행했습니다. 

최종 목표는 애플리케이션 서버 3대를 두고 앞에 로드밸런스 서버 1대를 두는 것으로 하고있습니다.
>근데 우선 결과를 단계적으로 지켜보고 싶어서 `애플리케이션 서버 2대`를 먼저 두고 `로드밸런스 서버 1대`를 둬보도록 했습니다.

### 원하는 그림

![](https://velog.velcdn.com/images/97gkswn/post/9edd56a9-5031-469d-acf1-66e5a0adbbe5/image.png)

## 우선 서버 3대를 생성해보자
[네이버 클라우드](https://www.ncloud.com/)로 가서 VPC를 하나 생성했습니다.
- VPC란?
VPC(Virtual Private Cloud)는 퍼블릭 클라우드 환경에서 사용할 수 있는 고객 전용 사설 네트워크입니다.

네이버에서 저보고 사용하라고 가상의 네트워크 공간을 배정해줬다고 생각하시면 됩니다.
>그리고 그 공간에 서버를 총 3대를 생성했습니다.
![](https://velog.velcdn.com/images/97gkswn/post/b913d95d-9c2f-47d1-9964-58cfb2a90769/image.png)
이 2대 중에서 1대를 앞에 세워둘 로드밸런스 서버로 이용할 예정입니다.

서버를 생성할 때는 https://yelog.netlify.app/deploy/ 이 분의 블로그를 참고했습니다!

서버 3대 각각 프로젝트를 배포해야 하므로 각각의 public ip(공인ip) 가 필요하다고 생각해서 3개의 public ip를 만들어서 각각 서버에 할당해줬습니다.

추가적으로 인바우드 규칙에 들어가서 필요한 Port들을 열어줬습니다.
네이버 클라우드에선 ACG라는 곳에서 설정을 하더라구요

## DB server 생성
제 프로젝트는 현재 데이터베이스를 mysql를 사용하고있는데요
네이버 클라우드에서 DB server를 생성해서 해당 DB server의 엔드포인트를
저의 spring 프로젝트 application.yml 부분에 설정해줘서 연동이 되게끔 했습니다.  
`이 때 엔드포인트는 DB server의 public ip를 의미합니다.`
```
// application.yml

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://{엔드포인트}:3306/lionz?serverTimezone=Asia/Seoul&characterEncoding=UTF-8
    username: {DB서버 만들 때 설정한 username}
    password: {DB서버 만들 때 설정한 password}
```

## 프로젝트 배포
[프로젝트 배포방법](https://velog.io/@97gkswn/Lionz-%EB%B0%B0%ED%8F%AC-aws)을 이용해 각각 두 대의 서버에서 같은 프로젝트를 배포해줍니다.
배포한 후 http://도메인 에서 https://도메인 으로 바꿔줍니다.


## ⚡️Nginx 서버 생성 (로드밸런스 서버 생성)
거창하게 Nginx서버라고 말했지만 사실 그냥 앞서 만든 일반 서버에 Nginx를 설치하고 설정을 좀 건드려 준다고 보면 됩니다.

총 3대의 서버를 생성하면 2대를 애플리케이션 서버 (프로젝트 올리는 곳)와 1대를 로드밸런스 서버로 한다고 했죠?
이제 그 한 대의 로드밸런스 서버를 만들 차례입니다!

### Nginx 설치
```
$ sudo apt update
$ sudo apt install nginx
```
아직 사용하지 않은 한 대의 서버에 접속해서 Nginx를 설치해줍니다.

```
$ service nginx start 
```
로 Nginx를 실행해주고

>Nginx가 제대로 켜졌는지 보려면 `ps -ef | grep nginx`를 입력해줍니다.
![](https://velog.velcdn.com/images/97gkswn/post/6bbbb21e-295a-4f5b-af98-2c15ea376155/image.png)
위와 같이 master process와 worker process가 있다면 정상적으로 켜져있다고 볼 수 있고

>좀 더 확실한  방법은 
`service nginx status`를 입력해보면 됩니다.
![](https://velog.velcdn.com/images/97gkswn/post/55ad41b3-5f33-4a5f-bff3-b3282fb301be/image.png)
위와 같이 초록불로 active로 되어있으면 정상적으로 실행된 것 입니다.

### ⚡️Nginx 로드밸런스 설정
Nginx는 http요청이 오면 site-enabled 폴더안의 파일들을 기준으로 로드밸런싱을 한다고 합니다.

```
$ cd /etc/nginx/sites-enabled
```
따라서 sites-enabled 폴더로 이동해줍니다.
<br>

```
$ rm -rf default 
```
이동해보면 안에 default파일이 존재할 겁니다.
기본적으로 default파일대로 작동을 할텐데 이 파일을 지우고 저희만의 설정대로 손봐주겠습니다.
<br>
```
$ vi 원하는이름      
```
원하는 파일이름을 생성해줍니다.
>그리고 그 안에 내용을 적어야 하는데 다음과 같은 내용을 작성하면 됩니다.
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
- 프론트 단(clinet)에서 오는 모든 요청은 80번 포트로 들어오게 됩니다.
따라서 `listen 80`으로 해주는 것이고
- 그 80번 포트로 들어온 요청들을 저 위의 `upstream안의 세 개의 서버`로 분산해서 보내주게 됩니다.
- upstream이름은 `원하는이름`을 해도 되는데 아래의 proxy_pass 뒷 부분에도 동일하게 `원하는이름`을 해야합니다.
- 마지막으로 `원하는 알고리즘`은 `방금 온 요청을 upstream안의 서버들 중에서 어느 서버로 보내냐를 정하는 알고리즘`을 의미합니다.
아무것도 적지않고 진행하게 되면 `라운드 로빈`으로 작동하게 됩니다.

### 설정 변경사항 적용

```
$ nginx -s reload
```
위의 내용을 저장하고 나와서 위의 명령어를 입력해줍니다.
- 주의할 점은 위의 명령어를 입력하려면 nginx가 실행중이어야 한다는 것입니다!

저는 위의 명령어를 입력했는데 
nginx: [error] open() ‘/usr/local/var/run/nginx.pid’ failed (2: No such file or directory) 이런 에러가 나와서 애먹었습니다..
https://its-blog.tistory.com/162 
위의 분의 블로그를 참고하여 해결했습니다.

## 로드밸런스 서버가 잘 올라갔나?
바로 위에서 한 것처럼 `nginx -s reload` 명령어가 정상적으로 먹혔다면 
Nginx서버가 잘 올라갔어야 합니다.
확인하는 방법이 있습니다.
> Nginx올린 서버의 `public IP주소:80`를 url에 입력해보면 됩니다.
이 때 뒤에가 왜 `:80`이냐면 바로 직전에 저희가 설정 파일에서 `listen 80`을 의미합니다.

![](https://velog.velcdn.com/images/97gkswn/post/8a9cb7c9-616f-471c-9ab1-3682394033e4/image.png)
제대로 올라갔다면 위와 같이 Welcome to nginx! 와 같은 메시지가 나올 것 입니다.

## 로드밸런스 서버가 제 기능을 하나보자
지금 lionz-3서버가 로드밸런스 서버고 뒤에서 lionz-1과 lionz-2서버는 요청이 오기를 기다리고 있을겁니다.
그럼 lionz-3서버의 public IP주소:80로 요청을 보내면 lionz-1과 lionz-2에 나눠져서 요청이 들어가야겠죠?

300명의 사용자가 30초동안 요청을 보낸다고 가정하고 부하를 줘보겠습니다.
```
k6 run --vus=300 --duration 30s scenario.ts
```
![](https://velog.velcdn.com/images/97gkswn/post/e7bedc1c-0a74-4079-88da-3efc8cc071fa/image.png)

8695개가 완료되었다고 떴습니다.
이제 Grafana로 가서 직접 그래프로 보겠습니다.
![](https://velog.velcdn.com/images/97gkswn/post/b65d7e60-0d9c-4d8e-a22e-be2903ab443f/image.png)

부하를 줬던 시점에 두 대의 서버가 골고루 나눠서 요청을 처리하는 것을 확인할 수 있습니다.
원래 로드밸런스를 하지 않았을 때 300명의 사용자가 30초동안 요청을 보내는 시나리오에서 꽤 많은 수의 요청들이 거부되었습니다.
지금 한 대의 서버가 0.5~0.6의 CPU사용률로 실행됐던 것을 보면 단 한 대의 서버였다면 에러가 나면서 요청들이 완벽히 처리될 수가 없는게 당연하다는 생각을 하게 됩니다.

이렇게 로드밸런스가 제대로 적용이된 것을 확인하면서 포스팅을 마치겠습니다! 
다음에는 로드밸런스 서버 1대, 애플리케이션 서버 3대를 둬서 vus를 1000으로 설정해서 보내보겠습니다.

## 참고
- https://access.redhat.com/documentation/ko-kr/red_hat_enterprise_linux/8/html/deploying_different_types_of_servers/configuring-nginx-as-an-http-load-balancer_setting-up-and-configuring-nginx

- https://hudi.blog/load-balancing-with-nginx/

- ChatGPT 3.5
	