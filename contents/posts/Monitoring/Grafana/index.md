---
title: "Grafana로 Server 모니터링하기 (with 프로메테우스)"
description: "Grafana로 Server 모니터링하기 (with 프로메테우스)"
date: 2023-08-25
update: 2023-08-25
tags:
  - Grafana
  - Prometheus
  - 모니터링

---

본 포스팅은 Mac OS를 기준으로 작성 했습니다.

## 개요
Grafana를 이용해 서버를 모니터링 할 수 있게 설정하는 방법은 이미 꽤 많은 사람들이 자세하게 작성해 놓은 블로그들이 있습니다.
따라서 제가 또 적기보다는 나중에 제가 까먹었을 때 보고 참고하거나 다른 분들도 뭘 봐야할지 모르겠고 설정할 때 큰 흐름을 모르실 때 참고할 수 있게끔 적어보려고 합니다.

## 모니터링 원리
우선 Actuator , 프로메테우스(Prometheus), Grafana 이 3개가  필요합니다.
최종적으로 우리 눈으로 Grafana에서 직접 그래프로 관찰할 수 있게 될텐데
이것의 원리를 알고가는게 좋을 것 같습니다.

> - 모니터링 원리의 큰 흐름
1. Spring boot의 actuator 라이브러리라는 친구가 현재 내 프로젝트의 `메트릭 정보`를 노출시킵니다.
2. 프로메테우스는 `actuator가 노출시킨 메트릭 정보` 를 수집하고 저장합니다.
3. 마지막으로 프로메테우스가 수집한 정보들을 Grafana에 표시하여 시각화 할 수 있습니다. 이런 방식으로 저희가 모니터링을 할 수 있는 겁니다!

- 메트릭 정보?
 -> 메트릭 정보는 시스템이나 애플리케이션의 상태와 동작에 관한 측정값이나 데이터를 말합니다.
 -> CPU 사용량, 메모리 사용량, 요청 처리 속도, 응답 시간 등등..

## 나도 해보자

### actuator
#### 도메인/actuator에 접근 가능하게 해주기

우선 저의 프로젝트(Lionz)에는 로그인 기능이 있어서 Token이 없으면 다른 어떤 api에 접근할 수가 없습니다. 
하지만 모니터링 할 때도 Token이 필요하다면 굉장히 불편하겠죠?
따라서 Token이 없어도 접근할 수 있게 설정해주도록 하겠습니다.

```
.requestMatchers("/actuator/**").permitAll()
```

프로젝트 파일의 SecurityConfig파일에 위와 같이 `내도메인/actuator/~` 의 url에 접근이 가능하도록 설정해줬습니다.


#### 의존성 추가 (build.gradle)

```
//actuator
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```
build.gradle에 의존성을 추가해줍시다.

### 프로메테우스
#### 설치
```
$ brew install prometheus
```
맥북에서 터미널을 열어 위의 명령어를 입력해 프로메테우스를 설치해줍시다.

#### 설정1 (prometheus.yml)
```
$ vi $(brew --prefix)/etc/prometheus.yml
```
설치가 완료되면 prometheus.yml이라는 파일이 생성됩니다.
위의 명령어를 통해 접근해줍시다.

```
//prometheus.yml

global: 
  scrape_interval: 10s  // 매트릭 수집 주기
 
scrape_configs:
  - job_name: "원하는 job_name"
    metrics_path: '/actuator/prometheus'
    static_configs:
    - targets: ["localhost:8083"]
   ```
  
위와 같이 작성해주시면 되는데 targets 부분은 본인의 프로젝트를 배포했던 서버의 IP나 도메인을 적어주시면 되겠습니다.

#### 설정2-의존성 추가 (build.gradle)

```
implementation 'io.micrometer:micrometer-registry-prometheus'
```    
build.gralde에 의존성을 추가해줍시다.

#### 재시작

```
$ brew services start prometheus
```

설정을 변경하였다면 위의 명령어를 입력해 프로메테우스를 재시작해줍니다.

#### 확인

프로메테우스가 사용하는 기본 포트는 9090입니다.
따라서 아까 prometheus.yml에 targets 부분에 썼던 본인의 도메인에 9090을 붙여서 접근하시면 됩니다.
`https://도메인:9090`

![](https://velog.velcdn.com/images/97gkswn/post/502c2f96-a659-432d-853b-80e19abf81f1/image.png)

url을 입력해 들어갔다면 위와 같이 Status->Targets를 눌러줍니다.
<br>
![](https://velog.velcdn.com/images/97gkswn/post/4b24d23b-aac2-45e0-ae41-3e858d7badeb/image.png)
이렇게 초록불이 뜨면 성공입니다!

### Grafana
#### 설치
```
$ brew update
$ brew install grafana
```
터미널을 켜서 위의 명령어를 입력해서 Grafana를 설치해줍니다.

#### 실행

```
$ brew services start grafana
```
위의 명령어를 입력해 Grafana를 실행해줍니다.
실행이 제대로 됐다면 `localhost:3000`으로 접근해줍니다.

#### 확인
`localhost:3000`으로 접근했다면 아래와 같이 뜰텐데요!
![](https://velog.velcdn.com/images/97gkswn/post/3d5faacb-0886-4de9-8b0c-ef4d99b0ab64/image.png)
처음에는 그냥 admin/admin을 통해 로그인하시면 됩니다.

이후의 과정은 https://ttl-blog.tistory.com/1366#%F0%9F%A7%90%20%EA%B7%B8%EB%9D%BC%ED%8C%8C%EB%82%98%EC%99%80%20%ED%94%84%EB%A1%9C%EB%A9%94%ED%85%8C%EC%9A%B0%EC%8A%A4%20%EC%97%B0%EB%8F%99-1
여기에 잘 정리되어 있으니 따라하시면 됩니다!



## 참고
actuator
https://hudi.blog/spring-boot-actuator-prometheus-grafana-set-up/

프로메테우스 (prometheus)
https://engineering.linecorp.com/ko/blog/armeria-prometheus-monitoring

 Grafana
https://grafana.com/docs/grafana/latest/setup-grafana/installation/mac/

https://ttlblog.tistory.com/1366#%F0%9F%A7%90%20%EA%B7%B8%EB%9D%BC%ED%8C%8C%EB%82%98%EC%99%80%20%ED%94%84%EB%A1%9C%EB%A9%94%ED%85%8C%EC%9A%B0%EC%8A%A4%20%EC%97%B0%EB%8F%99-1
