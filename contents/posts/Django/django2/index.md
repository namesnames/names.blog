---
title: "[Django] Tips"
date: 2022-05-20
update: 2022-05-20
tags:
  - Django

series: "Django"
---

## 1.DEBUG=False
DEBUG = False
로 변경하면 아래와 같은 에러가 발생
CommandError: You must set settings.ALLOWED_HOSTS if DEBUG is False.

DEBUG = True 일때는 상관없지만 DEBUG = False 가 되면 접속가능한 호스트를
ALLOWED_HOSTS 에 추가해줘야 함



ALLOWED_HOSTS = ['abc.com']
 abc.com으로 들어온 요청만 받음

이외의 요청이 들어오면 장고가 자동으로 차단

모든 호스트가 접근할 수 있게  *을 넣어준다.

ALLOWED_HOSTS = ['*']

## 2. source 명령어의 의미


- source 명령어를 아래처럼 activate 파일을 실행시켜서 가상환경을 실행시키기 위해 사용한다.
source venv/Scripts/activate
> 리눅스에서 특정 파일을 수정한 후 저장한 뒤에 재부팅해야 하는 과정을 생략 가능
> 재부팅 과정을 생략하고 새로운 변경 내용을 적용 가능하다.
> git bash를 사용하면 window 에서도 리눅스의 명령어를 사용 가능하다.


source 는 bash 의 내부 명령어로, source 뒤에 오는 파일을 실행시키는 기능을 한다.

참고로 . 을 사용해서도 파일을 실행시킬 수 있다.



## 3. static file
>-이미지, CSS, HTML, js 같이 해당 내용이 고정되어, 응답할 때 별도의 처리 없이 파일 내용을 그대로 보여주면 되는 파일
-즉, 사용자의 요청에 따라 내용이 바뀌는 것이 아니라 요청한 것을 그대로 응답하면 되는 파일

## 4. static method(정적 메소드)
클래스 내의 함수의 첫 번째 인자에 self를 쓰지 않는 방법
클래스 내의 함수 위에 @staticmethod 추가
객체 생성없이 사용 해야함. (클래스명.함수명(인자))
```
class Calculator:

    @staticmethod
    def plus(a,b):
        return a+b

print(Calculator.plus(5,15))

#결과
20
```