---
title: "[Django] 블로그사이트 기능추가"
description: "Django 블로그사이트 기능추가"
date: 2022-06-28
update: 2022-06-28
tags:
  - Django

series: "Django"
---

회원가입기능을 추가해야 하는데 그 과정에서 사용자로부터 받을 정보들이 기본 User table에 있는 것 말고도 MBTI와 닉네임을 받아야 하는 상황
accounts  앱에서 models.py 에서 AbstractUser를 상속받아 새로운 모델을 만듦

![](https://velog.velcdn.com/images/97gkswn/post/476a23c8-6395-4a6c-a39e-2cfec791cb7a/image.png)

![](https://velog.velcdn.com/images/97gkswn/post/7de4d706-6a50-4860-8660-f389a10df22b/image.png)

위처럼 프로젝트 디렉토리의 settings.py에 
AUTH_USER_MODEL = accounts.myUser 추가

## 중요한 점
파이썬은 대소문자를 구분한다.
이로 인해 자주 오류를 범하게 되니 항상 신경써야한다.
A(대문자)에는 상수 를, a(소문자) 는 변수 를 넣어준다.

## 회원가입 만들기
![](https://velog.velcdn.com/images/97gkswn/post/81f8561b-4599-4efb-aecc-e66a0007b822/image.png)

![](https://velog.velcdn.com/images/97gkswn/post/ab1d728b-4bb8-4673-87d4-a1c5dac70654/image.png)

"회원가입"이라는 버튼을 누르게 되면 form data가 action쪽 url로 전달
 
 ![](https://velog.velcdn.com/images/97gkswn/post/038d2ad2-b83b-4e7a-8519-79d98837efce/image.png)

request.POST로 각각의 받은 데이터들을 변수에 저장해주고
myUser.objects.create_user() 로 user객체의 username,password들에 변수를 다시 넣어준다.

(urls.py도 추가해줘야함)

## 로그인 구현
![](https://velog.velcdn.com/images/97gkswn/post/95f3f6b4-c30c-4b7a-8a9b-8db2af34061e/image.png)

![](https://velog.velcdn.com/images/97gkswn/post/5fa45a17-1d52-4ee9-b9ed-7f3a1657886d/image.png)

>![](https://velog.velcdn.com/images/97gkswn/post/4b06458d-0409-47e8-bccd-d166519c9ddc/image.png)
>장고에 로그인 관련된 것들이 이미 구현되어있어서 auth 를 import해주고 
auth.authenticate 와 auth.login  , auth.logout 를 그냥 써주면 됨
auth.authenticate는 인자에 username,password를 넣어주면 이미 있는 계정인지 확인을 해준다.

## 로그아웃 구현
![](https://velog.velcdn.com/images/97gkswn/post/e72a40fe-815d-4f32-9afa-f5c9096b362c/image.png)

그냥 auth.logout 사용해주면 됨

## MBTI 테스트페이지 만들기
![](https://velog.velcdn.com/images/97gkswn/post/d7b8a851-e640-4221-a6d1-0badc3459fee/image.png)
라디오타입으로 버튼을 만들고 

![](https://velog.velcdn.com/images/97gkswn/post/4f21bfb6-de58-4980-8329-720dc825a2d1/image.png)
request.POST로  


 ### 다양한 input type 요소의 타입들
![](https://velog.velcdn.com/images/97gkswn/post/88a24742-0e3f-4a50-9e73-1f600665cf24/image.png)

