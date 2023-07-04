---
title: "[Django] Popit 프로젝트"
description: "Django Popit 프로젝트"
date: 2022-09-02
update: 2022-09-02
tags:
  - Django

series: "Django"
---

멋쟁이사자처럼 인하대10기 해커톤 POPIT 서비스 프로젝트(백엔드파트)
# 백엔드 팀원과 git 활용방법
우선 같이 model을 구현하고(+공통작업) git main에 올린다
각자 clone을 하고 코드의 변경사항(기능추가 등) 이 있으면 branch를 파고 branch에 push를 한다. 
push를 했으면 pull request를 한다
그리고 코드를 합칠 때에는 pull request를 하지 않는 다른 팀원이 코드를 한 번 확인해보고 merge버튼을 누른다

merge를 할 때 같은 파일에서 코드 line이 겹치면 충돌이 일어난다
따라서 우선은 기능별로 나누어서 아예 다른 파일에 각자 작업을 하는 방식을 사용해야 하고 혹시나 같은 파일에서 작업 할 일이 생긴다면 코드 line을 다르게 해서 작업하고 merge를 해야 충돌이 일어나지 않는다. 
# 구현한 기능들(본인이 중점적으로)
## 팔로우/언팔로우 기능
우선 User 모델에서 팔로우 필드를 아래와 같이 ManyToManyField로 선언해주었다
![](https://velog.velcdn.com/images/97gkswn/post/8478d35e-dc0c-4299-9496-3157743fa431/image.png)
views.py에 아래와 같이 작성을 해주었는데
Post요청(팔로우 버튼을 눌렀을 때)이 들어오면 followers에 요청한 user를 add하는 방식이다
![](https://velog.velcdn.com/images/97gkswn/post/7a45b4c6-6e62-423c-a1d6-bbd7d8afdf13/image.png)

## 게시글(pop) 좋아요 버튼
![](https://velog.velcdn.com/images/97gkswn/post/b2476b33-485d-4e62-a243-8e504f59494a/image.png)
이것도 팔로우랑 비슷한 방식으로 구현했는데 게시글(pop) 모델에 있는 
manytomany로 설정되어있는 user_who_like에 좋아요 누른 user를 add하는 방식으로 구현했다

## 게시글(pop) 저장버튼
![](https://velog.velcdn.com/images/97gkswn/post/66c6768f-1c25-4698-9e03-1fca51232ea7/image.png)


## 내가 저장한 게시글(pop)만 보기
![](https://velog.velcdn.com/images/97gkswn/post/4e40eec8-4051-4da8-ad3f-2a7f8455122b/image.png)
Get요청이 들어오면 Pop.objects로 모든 pop객체를 가져오는데 filter위와 같이 걸어주었다
pop에 저장되어 있는 save_user가 요청한 사람인 것만 가져오도록 filter를 걸어준것이다

## 배포(Docker)
Linode 와 Docker를 이용하여 배포했다
(docker 버전확인: docker --version)
pip install gunicorn 설치하고
pip freeze > requirements.txt 로 생성하고 (이때 txt안에 gunicorn 없으면 넣어줘야함) 
플젝 드가면 바로 있는 디렉토리에 (앱 있는 위치) 
Dockerfile 이라는 파일 하나 만들고
Linode 창 키고 
git clone SSH주소 (클론하고)
ls 해서 프로젝트 안으로 들어가고(cd)
docker login 하고 (Linode로는 안해도 된다고 함)
docker build -t hi3 . (쩜 하고 점프하고 엔터쳐야함)
(docker images : 만든 도커 이미지들 쭉 나옴)
(docker rmi 이미지 이름 : 도커 이미지 삭제)
`docker run --name 컨테이너이름 -d -p 8000:8000 hi3` 치고
(964f67de6f2db6048b247ab403591b5def0e29d76eabd26188b707a89b00a84c 이런거 나오면
배포 성공임)
```
import os
API_KEY=os.environ.get('API_KEY')
```
만약 이런식의 코드가 있다면 환경변수를 사용한거라서
도커 컨테이너 내에서 환경 변수를 설정하려면, -e 또는 --env 플래그를 사용합니다. 예를 들어, -e API_KEY=your_api_key와 같이 사용하여 API_KEY 환경 변수를 설정할 수 있습니다.
(docker ps -a로 확인)
그래서 저기서 API_KEY에 키값 넣어주려면 
`docker run --name 컨테이너이름 -d -p 8000:8000 -e API_KEY=your_api_key 도커이미지이름` 을 하면 됨

Linode 홈페이지 들어가면 IP주소 확인할 수 있음
139.162.67.184

<배포 중지>
docker stop 컨테이너이름
docker rm 컨테이너이름 (컨테이너 삭제)
docker ps -a 로 확인

코드 바뀌면 저장하고 깃에 푸시하고 Linode에 다시 풀 받고 배포 다시 진행

## 회고
백엔드 개발자(다른 개발자도..)는 본인의 개발만 잘하면 되는것이 아니다
프론트 개발자와의 소통이 매우 중요하고 서로 개발한 것을 연결할 때 문제가 굉장히 많았는데 나도 axios를 좀 공부해서 도움이 되어야 겠다고 생각했다.
또 프로젝트를 시작할 때 프론트와 백이 같이 모여 먼저 API를 짜고 그것에 맞춰서 개발하는 것이 훨씬 효율적이라고 느꼈다
이렇게 하지 않으면 이후에 API 깔금하게 정리가 되지않고 직관적이지 못해서 좋지 않다. 또한 소통에 있어서도 더 원활해질 수 있다

