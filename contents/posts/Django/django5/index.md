---
title: "[Django] Restframework serializer"
description: "Django Restframework serializer"
date: 2022-07-13
update: 2022-07-13
tags:
  - Django

series: "Django"
---

CRUD의 기능을 구현할 때 유용하게 쓸 수 있는 것들이 있음
그 중 Mixin 이랑 GenericAPIView 가 있는데
GenericAPIView는 CRUD(생성/읽기/수정/삭제)에서 공통적으로 사용되는 속성을 제공하고 
Mixin은 CRUD 중 특정 기능을 수행하는 메소드를 제공함
우리는 GenericAPIView를 이용해 간단한 블로그 글 올리기 및 삭제를 해볼것임
(그전에 pip install djangorestframework 해서 패키지 설치해줘야함)
우선 Blog 모델을 만들고 BlogSerializer 라는 시리얼라이저를 serializers.py 에 만들어줄것임
>models.py
![](https://velog.velcdn.com/images/97gkswn/post/b8dab921-fde7-461b-b8af-8825bae69923/image.png)

<br/>

>serializers.py
![](https://velog.velcdn.com/images/97gkswn/post/35d56be7-58d7-4070-8817-2de8432d1f16/image.png)


무슨 뜻이냐면 Blog모델에있는 데이터들을 응답으로 보내줄 건데 그 기본형태를 BlogSerializer라고 정의한것임
즉, 응답을 보낼때 fields에 있는 것들이 응답이 가는거임
만약 저기 fields에 Blog에 있는 데이터들인 text,writer,password 등 중에서 text만 쓰면 text만 응답으로 가는것임


>views.py
![](https://velog.velcdn.com/images/97gkswn/post/64156d34-570b-4e3b-ad54-1b88ce8ea3c6/image.png)
위에 
from .serializers import BlogSerializer
from .models import Blog 적어주고
![](https://velog.velcdn.com/images/97gkswn/post/eab55e51-f94c-476f-8b13-566cb1eba3a1/image.png)
블로그 글들을 보여줄 BlogList클래스에 generics.ListCreateAPIView 상속해주자
일단 generics 쓸 때 기본적으로 써줘야 할 것들이 있음
queryset=Blog.objects.all()
serialier_class = BlogSerializer
lookup_filed = 'id' 

> 블로그 detail을 보여줄 BlogDetail 클래스
![](https://velog.velcdn.com/images/97gkswn/post/335f2bf4-ed0a-4f3d-91eb-e591a1c8888f/image.png)
여기서는 글을 삭제해야 하므로 
generics.RetrieveUpdateDestroyAPIView 를 상속받아야함

 BlogDetail 클래스를 보면 
RetrieveUpdateDestroyAPIView 들어가보면 
     def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs) 있고
    retrieve 들어가보면 
    def retrieve(self, request, *args, **kwargs):
        instance = self.get_object()  #Blog.objects.get(pk=pk)를 의미
        serializer = self.get_serializer(instance)
        return Response(serializer.data) 이 있다.
    이 때 get_object는 소스코드를 확인해보면 queryset과 pk값을 인자로 받아서
        queryset.filter(pk=pk)로 queryset을 뽑고,
    obj = queryset.get()으로 객체만 뽑아서 리턴해 주는 메소드임
    => 결국, 위 코드는 Blog.objects.get(pk=pk) 리턴함

postman 을 이용해서 확인을 하는데
GET/board/id 했을 때 pk가 id인 블로그 글이 나와야 하므로 url을 
![](https://velog.velcdn.com/images/97gkswn/post/92370f31-12b1-4066-ad81-02768e835fa9/image.png)
path('board/<int:pk>,views.BlogDetail.as_view()) 해줬다
처음엔 <int:id>를 해줬었는데 실행이 제대로 안됐다
이제 보니 위의 설명대로 get_object()가 pk를 인자로 받아서 진행하는데 내가 id를 넘겨줘 버리니 안돌아간것같다. 그래서 <int:pk>로 바꿔줌

>다시 BlogDetail 보면
![](https://velog.velcdn.com/images/97gkswn/post/335f2bf4-ed0a-4f3d-91eb-e591a1c8888f/image.png)
블로그 글 보여주는 GET은 내장함수가 있으니 가만두고
DELETE할 때 password가 일치했을 때만 삭제해야하므로
조금 수정을 했어야했다.

as_view()에 대해서..


