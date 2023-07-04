---
title: "[Django] 파이썬 배경지식"
description: "Django (파이썬 배경지식)"
date: 2022-05-16
update: 2022-05-16
tags:
  - Django

series: "Django"
---

장고=웹프레임워크=웹서비스를 쉽게 만들어주는 기계
# 파이썬 배경지식
## 딕셔너리 
대응이되는 데이터를 표시하고싶을때
키워드에 대응되는 value들을 찾고자
Nx2 표에 쓰고싶을때 종종 사용
key는 중복되어서도 변해서도 안됨
![](https://velog.velcdn.com/images/97gkswn/post/e4b23d3d-5a4e-47cc-b619-e9e8cee9085c/image.png)
새로운 데이터 쌍 추가는 Val[key3]=value3 이런 식으로 해주면 됨
## 예외처리
 파이썬 에러 두가지
1.문법에러(파싱 에러)
실행 자체에 영향을 주는 치명적오류
2.예외
프로그램 실행 자체를 멈추지는 않는 오류
0으로 나누기,정의되지 않은 이름 사용 등등
오류를 핸들링 by <try~except>
### (1)
```
try: 
     오류가 생길 여지가 있는 코드 (일단 시도는 해 볼 코드)
except 발생오류:
      발생오류가 발생했을때 실행할 코드
```
![](https://velog.velcdn.com/images/97gkswn/post/034629cb-9cbf-4799-a673-12393114764e/image.png)
### (2)
```
try: 
     오류가 생길 여지가 있는 코드 (일단 시도는 해 볼 코드)
except:
      발생오류가 발생했을때 실행할 코드
```      
그냥 except만 쓸 경우는 try에서 어떤 오류가 발생했던지 간에 except 아래의 코드를 실행시키라는 말임
![](https://velog.velcdn.com/images/97gkswn/post/00f9ce1b-0f88-4575-bb2e-b4308130c21b/image.png)
### (3)
```
try: 
     오류가 생길 여지가 있는 코드 (일단 시도는 해 볼 코드)
except:
      발생오류가 발생했을때 실행할 코드
finally:
      예외가 발생 했든 안했든 최종적으로 무조건 실행 할 코드
```

## 객체지향프로그래밍 이란?
세상에 있는 모든 것들을 프로그래밍 하고 싶다!
이 세상 모든 대상들은 상태와 동작으로 표현 가능
상태와 동작은 변수와 함수로 표현 가능
근데 모든 대상들은 한 두개가 아니므로 변수와 함수를 여러 개 만들어야함
즉 변수와 함수를 한번에 여러개 정의할 수 있는 방법==객체지향프로그래밍 이다
![](https://velog.velcdn.com/images/97gkswn/post/f4839102-d451-470f-a26a-1eab465deafd/image.png)
즉 아래처럼 왼쪽처럼 적(enemy)라는 틀(class)을 만들고 오른쪽처럼 여러명의 적들(객체)을 만들어 내는 것 
![](https://velog.velcdn.com/images/97gkswn/post/5da4137b-8cbb-4490-840c-0fa5deca4c62/image.png)

![](https://velog.velcdn.com/images/97gkswn/post/aaa8c930-26be-4988-9f2d-e9774df4f1bc/image.png)
Data 디렉토리(폴더) 는 저 모듈들을 가지고 있는 패키지다.

모듈 : 파이썬으로 정의된 파일(가장 작은단위)
서로 다른 모듈을 갖다 쓰려면 import
패키지( 모듈의 집합)
라이브러리
쓸만한 기능들을 미리 모듈/패키지로 만들어 놓은 것 (미리 준비된 모듈/페키지 코드뭉치)
파이썬 스탠다드 라이브러리(내장되어있음)
파이썬 패키지 인덱스 (사람들이 업로드)
pip 명령어를 이용해 설치,삭제,조회 등등 가능
URL 정보의 위치
HTTP 통신방법
GET 갖다줘(html정보)
POST 데이터를 처리해달라는 요청
HTML 정보자원을 우리가 볼수있도록 되어있고 
 web service
HTML과 URL을 미리 준비해놓고 사용자 요청에 대한 응답을 보낼 수 있는 프로그램
Web Framework
프로그래밍으로 웹을 만들 수 있지만 만드는 과정이 정형화 되어있어서 더 효율적으로 만들기위해 미리 만들어놓은 웹개발의 기능단위
라이브러리 vs 프레임워크
프레임 워크는 공장과 같다
하라는 대로만 잘 따라서 하면 제품이 만들어지듯 이미 설계까지 만들어진 구조에 쓰라는 방식대로 쓰면 목적을 달성 할 수 있는
라이브러리는 도구와 같다
뭔가를 만들기 위해 도구를 가져다 쓰는 느낌

MVC
model 데이터베이스와 상호작용 담당
view 사용자 인터페이스 담당
controller 웹 서비스 내부의 논리 담당
장고의 디자인패턴=MTV
MTV
model 데이터베이스와 상호작용 담당
template 사용자 인터페이스 담당
view 웹 서비스 내부의 논리 담당

사용자가 instagram.com 입력시 GET요청한거임 controller는 HTML 보여주라고 명령
글 게시를 위해 POST요청하면 controller를 통해 DB에 글을 저장하자고 결정하고 model을 통해 DB에 저장  

가상환경
독립적인 개발환경을 만들기 위함
독립적인 개발환경이 아니라 컴퓨터 전체환경에서 진행된다면 여러개의 프로젝트를 진행할때 각 프로젝트에 필요한 패키지들이 서로다른 패키지에 영향을 줄 수 있기때문에 필수

가상환경이라는 통 안에서 진행해서 통 외부에는 영향을 미치지 않게 하기 위함
가상환경 만드는 명령어
python -m venv 가상환경이름

가상환경 실행
source myvenv/Scripts/activate

가상환경 끄기

 django-admin startproject myproject
myproject라는 이름의 장고프로젝트 생성

__init__.py 가 속해있다는 것으로 폴더가 패키지라는 것을 알려줌
manage.py의 기능 
1.서버실행
python manage.py runserver
서버열기
ctrl c 누르면 서버꺼짐
2. Application 만들기
특정기능을 담당하는 어플을 만들어서 관리하는게 효율적이기 때문에 여러개의 어플리케이션을 만들어서 진행
python manage.py startapp 이름
어플 하나하나가 MTV 패턴을 가지고있음
어플들을 만들고 settings.py에 어플들을 등록해줘야함  INSTALLED_APPS리스트 안에 어플이름 넣어주면 됨
3.DB초기화 및 변경사항 반영(migrate)
python manae.py migrate
(이거 해야 관리자 계정 만들수있는듯)
4.관리자 계정 생성
python manage.py createsuperuser
해서 이름 비번 설정하고
python manage.py runserver
하고 사이트 뒤에 /admin 붙여서 접속하면 로그인창 나옴

setteings.py
Secret key = 비밀번호를 어떤식으로 암호화하는지 (절대 노출x)
 DEBUG (개발자용으로 킬것인지 배포용으로 킬것인지 결정)
true로 해놓으면 잘못된 url로 접속했을때 자세한 정보까지 나오지만 Flase로 하면 자세한 정보 없이 Not found정도만 나옴 (실제로 개발할때 False로 쓰자)
DATEBASES 
어떤 데이터베이스를 쓸지 그 위치는 어디에있는지 
#Internationlization
언어나 시간 설정
STATIC_URL
우리 웹서비스에서 미리 준비해둔 css,images등 들의 url을 적어줌

python -m venv myvenv
source myvenv/Scripts/activate
pip install django
django-admin startproject 이름
cd 이름/
python manage.py runserver
python manage.py startapp myapp

127.0.0.1 은 내컴퓨터를 지칭함
url요청이 오면 urls.py 에서 이런 url이 있는지 확인하고 그때 실행할 함수를 views.py~ 로 가서 실행
