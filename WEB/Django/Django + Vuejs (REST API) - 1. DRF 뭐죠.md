# Django + Vuejs (REST API) - DRF 뭐죠?

- 뷰(SPA) 클라이언트 사이드 + 장고 서버 사이드

- 설명

    <img width="906" alt="_2020-08-27__1 49 40" src="https://user-images.githubusercontent.com/60081217/91643023-b4dc7080-ea6a-11ea-83ac-e05123588ffc.png">

    - 클라이언트가 요청을 보내고 서버가 응답을 함
    - 요청 : URL(+a), 응답: HTML

    ### Django REST Framework

    - Model : 데이터를 구조화 하는 곳
    - View: 데이터가 흘러다니는 곳(CRUD)
    - Template: 데이터를 표시하는 곳

    ### API

    - 그냥 데이터만 주세요! 하고 시작된게 → API
    - 개발자가 쓸 수 있는 데이터

    ### 데이터 표기법

    - JSON
    - XML

    ### 최종 구조

    <img width="930" alt="_2020-08-27__2 04 25" src="https://user-images.githubusercontent.com/60081217/91643032-befe6f00-ea6a-11ea-8c6c-1d48952fadab.png">

- 가상환경 설정 (다까먹음 ㄱ-)
    - 먼저 폴더 하나 만듦

    ```bash
    $ python3 -m venv venv
    $ code .
    $ source venv/bin/activate
    ```

    - 배쉬창에 (venv)가 달린 걸 확인한다.

    ```bash
    $ pip list # 뭐가 깔려있는지 확인, 아마 뭐 없을걸
    $ pip install django==2.1.15
    ```

    - 장고 시작

    ```bash
    $ django-admin startproject {프로젝트 이름}
    $ cd {프로젝트 이름}
    $ python manage.py startapp {앱 이름}
    ```

    - AllowedHost, app등록

- faker → 대신에 더미데이터를 만들어 줌

    ```python
    $ pip install faker
    ```

- shell plus - django_extensions

    ```python
    $ pip install django_extensions
    ```

    - [settings.py](http://settings.py) → INSTALLED_APPS 에 추가

    ```python
    $ python manage.py shell_plus

    >>> from faker import Faker
    >>> f = Faker()
    >>> f.text()

    ctrl + D 로 shellplus끄기
    ```

- requirements.txt

    ```python
    $ pip freeze > requirements.txt
    $ pip install -r requirements.txt
    ```

## DRF(Django RestFramework)

- DRF(Django RestFramework)는 Django 안에서 RESTful API 서버를 쉽게 구축할 수 있도록 도와주는 오픈소스 라이브러리이다.
- REST란 간단히 URI는 자원을 표현하는데 집중하고, HTTP METHOD(POST / GET /PUT /DELETE)를 통해 행위(해당 자원을 제어하는) 에 대해 정의하는 방식의 아키텍쳐를 의미한다. 이와 같은 방식으로 적용된 API를 RESTful API 라고 한다.

### 모델링

- board/ models.py

```python
from django.db import models

# Create your models here.

class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

- DB에 모델링 저장

```bash
$ python manage.py makemigrations
$ python manage.py migrate
```

### 모델에 faker를 이용해서 dummy data만들기

- models.py

```python
from django.db import models
from faker import Faker

f = Faker()

class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    @classmethod
    # n개의 더미데이터를 만드는 classmate
    def dummy(cls, n):
        for _ in range(n):
            cls.objects.create(
                title = f.name(),
                content = f.text(),
            )
```

- shell plus 켜기 → `$ python [manage.py](http://manage.py) shell_plus`

```python
>>> Article.dummy(100)
```

### url 정리

- 프로젝트 urls.py

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('board', include('board.urls')),
]
```

- 앱 [urls.py](http://urls.py) 생성

```python
from django.urls import path
from . import views
 
 app_name = 'board'

 urlpatterns = [
     
 ]
```

### views. py → 이제 데이터를  HTML에 녹이지 않고 json형태 그대로 뽑아내자

- 기존의 방식
- articles 안에 data를 넣고 html에 전달한다.

<img width="651" alt="_2020-08-27__3 45 52" src="https://user-images.githubusercontent.com/60081217/91643033-c0c83280-ea6a-11ea-9f46-740549085083.png">

- 우리가 원래 썼던 `shortcut` 은 자주 쓰는 것들은 모아논것..
- `from django.http.response import JsonResponse` / `from django.http.response import HttpResponse`

### 1. 노가다 방법 → 추천 놉!

```python
# views.py
from django.shortcuts import render
from django.views.decorators.http import require_GET
from django.http.response import JsonResponse
from .models import Article

@require_GET
def article_list_json1(request):
    # queryset 
    articles = Article.objects.all()
    # json은 구조가 list, dictionary로 구성이 되어야 한다. 
    # 근데 queryset은 그 구조가 아님 
    # 그래서 for문을 통해 그 구조를 맞춰줘야함.
    data = []
    for article in articles:
        data.append({
            'article_id': article.id,
            'title': article.title,
            'content': article.content,
            'created_at': article.created_at,
            'updated_at': article.updated_at,
        })
    return JsonResponse(data, safe=False)
```

```python
# urls.py
from django.urls import path
from . import views
 
app_name = 'board'

urlpatterns = [
    path('json1/', views.article_list_json_1),
]
```

- 만약에 마지막에 `safe=False` 가 없는 상태라면 아래의 에러가 뜬다.

<img width="782" alt="_2020-08-27__4 07 42" src="https://user-images.githubusercontent.com/60081217/91643041-d178a880-ea6a-11ea-99d0-ecfc57282529.png">

- /boards/json1

<img width="975" alt="_2020-08-27__4 13 04" src="https://user-images.githubusercontent.com/60081217/91643042-d3db0280-ea6a-11ea-9a73-9f3971407b75.png">

→ 만약에 필드가 30개라면?  너무 복잡해짐..

### 2. django core serializer → 이것도 추천 안함

<img width="563" alt="_2020-08-27__4 26 33" src="https://user-images.githubusercontent.com/60081217/91643043-d63d5c80-ea6a-11ea-9aa6-56d5913d7b72.png">
<img width="762" alt="_2020-08-27__4 26 49" src="https://user-images.githubusercontent.com/60081217/91643045-d8072000-ea6a-11ea-8787-b2092d40a117.png">

→ 하나의 string으로 만들어 준다. (직렬화 해준다)

```python
#views.py
# django core serializer
@require_GET
def article_list_json_2(request):
    from django.core import serializers

    articles = Article.objects.all()
    # articles를 json으로 바꿔주세요~
    data = serializers.serialize('json', articles)

    return JsonResponse(data, safe=False)
```

```python
#urls.py
from django.urls import path
from . import views
 
app_name = 'board'

urlpatterns = [
    path('json1/', views.article_list_json_1),
    path('json2/', views.article_list_json_2),
]
```

- 결과
- 완전 스트링 값으로 나옴 ⇒ JsonResponse는 딕셔너리나 리스트를 받는 애지 스트링을 받는 애가 아님

<img width="983" alt="_2020-08-27__4 29 54" src="https://user-images.githubusercontent.com/60081217/91643071-1bfa2500-ea6b-11ea-9004-b900dffa9a2e.png">

```python
# django core serializer
@require_GET
def article_list_json_2(request):
    from django.core import serializers

    articles = Article.objects.all()
    # articles를 json으로 바꿔주세요~
    data = serializers.serialize('json', articles)
		
		# 이렇게 수정
    return HttpResponse(data, content_type='application/json')
```

<img width="987" alt="_2020-08-27__4 32 37" src="https://user-images.githubusercontent.com/60081217/91643074-1d2b5200-ea6b-11ea-8d6a-6e6cf2371ca4.png">

- Serialize는 원래 데이터를 덤프 떠서 한방에 때려 넣을 때 쓰는 아이이다. 이렇게 쓰는 애가 아니긴 함.

### 3. 두 개의 장점을 다 쓰는 거 → Django REST framework

[Home - Django REST framework](https://www.django-rest-framework.org/)

- 설치 :   `pip install djangorestframework`

- <img width="722" alt="_2020-08-27__4 56 11" src="https://user-images.githubusercontent.com/60081217/91643075-1ef51580-ea6b-11ea-8885-455fc94872e6.png">data 가 넘어 왔을 때 → 데이터 검증, html 만들어 주고, 한방에 다 해주고 → modelform과 비슷하다!
- 모델과 연동해서 한방에 가능, 커스터마이징도 가능

```python
$ cd {앱 이름}
$ touch serializers.py
```

- serializers.py

```python
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = '__all__'
```

- views.py

```python
from django.shortcuts import render
from django.views.decorators.http import require_GET
from django.http.response import JsonResponse, HttpResponse

from rest_framework.response import Response
from rest_framework.decorators import api_view

from .models import Article
from .serializers import ArticleSerializer

#rest framework
#rest framwork를 쓰려면 꼭 필요한 작업
#require_GET과 동일한 의미
@api_view(['GET'])
def article_list_json_3(request):
    articles = Article.objects.all()
    # many=True 는 하나가 아니라 여러개, queryset일때 이 옵션을 줘야함.
    serializer = ArticleSerializer(articles, many=True)

    #rest_framework의  serializer를 리턴하려면, Response
    return Response(serializer.data)
```

- urls.py

```python
from django.urls import path
from . import views
 
app_name = 'board'

urlpatterns = [
    path('json1/', views.article_list_json_1),
    path('json2/', views.article_list_json_2),
    path('json3/', views.article_list_json_3)
]
```

- 결과 사진

<img width="989" alt="_2020-08-27__5 12 05" src="https://user-images.githubusercontent.com/60081217/91643077-20bed900-ea6b-11ea-928a-e7246e26d849.png">