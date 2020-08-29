# :black_joker: Django + Vuejs (REST API) + Post man - 2. Django backend(login, logout, signup, create)

```python
$ python2 -m venv venv
$ touch .gitignore
$ pip install django==2.1.15
$ pip install djangorestframework
$ pip freeze > requirements.txt

$ django-admin startproject django_for_vue .
$ python manage.py startapp accounts
$ python manage.py startapp articles
```

- settings.py

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    #DRF
    'rest_framework',

    #My Apps
    'accounts',
    'articles',
]

...

AUTH_USER_MODEL = 'accounts.User'
```

- accounts/models.py

```python
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    pass
```

### 모델링

- articles/models.py

```python
from django.db import models
from django.conf import settings

# Create your models here.

class Article(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

```python
$ python manage.py makemigrations
$ python manage.py migrate
```

### url 정리하기

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', include('articles.urls'))
]
```

```python
from django.urls import path
from . import views

app_name = 'articles'

urlpatterns = [
    path('', views.article_list),
    path('create/', views.create_article),
    path('<int:article_pk>', views.article)
]
```

### Article → serializing하기

```python
$ touch articles/serializers.py
```

- serializers.py

```python
from rest_framework import serializers
from .models import Article

class ArticleListSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'created_at']

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = '__all__'
```

- views.py

```python
from django.shortcuts import get_object_or_404
from rest_framework.decorators import api_view
from rest_framework.response import Response

from .serializers import ArticleListSerializer, ArticleSerializer
from .models import Article

@api_view(['GET'])
def article_list(request):
    articles = Article.objects.all()
    serializer = ArticleListSerializer(articles, many=True)
    return Response(serializer.data)

@api_view(['GET'])
def article_detail(request, article_pk):
    article = get_object_or_404(Article, pk=article_pk)
    serializer = ArticleSerializer(article)
    return Response(serializer.data)

@api_view(['POST'])
def create_article(request):
    serializer = ArticleSerializer(data=request.data)
    # raise_exception은 if문을 통과하지 못한 애들한테 뭐가 잘못됐는지 말해줌
    if serializer.is_valid(raise_exception=True):
        serializer.save()
        return Response(serializer.data)
```

### POST MAN  확인하기

- Collections → New collections → django-for-vue
- django-for-vue → articles

<img width="1392" alt="_2020-08-27__5 52 10" src="https://user-images.githubusercontent.com/60081217/91643203-20730d80-ea6c-11ea-9f8f-20d45d04268d.png">

### accounts 인증

- 원래는 session을 썼다.
- 요청을 보낼 때 마다  브라우저가 가지고 있는 cookie 에 session_id라는  key값 안에 'sekyung'이란게 써있다! → user_id : 1번 임을 파악함
- 근데 세션인증방식은  장고 처럼 템플릿과 서버가 같은 포트 안에서 동작할 때만 가능한 것이다.
- 장고에서 로그인 성공하면 session_id 발급해주고, 로그아웃 하면 지우고 다 알아서 했었음

[Session](https://www.notion.so/31fb5da41c8445dcb59bbc9d094036c9)

### Token을 발급해주자 !

- 회원가입
    - Vue 에서 django로 요청한다.

    ```python
    "username": "neo",
    "password1": "1234",
    "password2" : "1234"
    ```

    - 검사한다 → 같은거 없는지, 1,2 같은지, 중복 어쩌고
    - user테이블에 정보를 쓴다
    - token이라는 테이블에 user_id, token을 쓴다 (세션과 유사)
    - vue에 응답을 준다 →  "token: sekyung"
    - 그럼 뷰에서는 토큰을 저장해준다.
- Request 보낼 때 Header에다가 "Authorization" : "Token sekyung"
- 그럼 장고에서 얘를 캐치해서 → 얘가 1번 유저인지 확인을 하는 거다.
- 토큰은 요청을 보낼 때 마다 헤더에 담아서 보내야 한다. → 근데 이거 일일히 다 못할 것 같다 → django-rest-auth

### 여기서 쓰는 게 django-rest-auth, django-allauth

`pip install django-rest-auth django-allauth` 

- django-rest-auth : login 과  logout
- django-all-auth : signup

[Welcome to django-rest-auth's documentation! - django-rest-auth 0.9.5 documentation](https://django-rest-auth.readthedocs.io/en/latest/)

[Authentication](https://www.django-rest-framework.org/api-guide/authentication/)

<img width="588" alt="_2020-08-27__7 54 05" src="https://user-images.githubusercontent.com/60081217/91643205-21a43a80-ea6c-11ea-9bbb-e1743522db9e.png">

<img width="453" alt="_2020-08-27__7 59 38" src="https://user-images.githubusercontent.com/60081217/91643207-236dfe00-ea6c-11ea-9461-8d4fefcbc85f.png">

- settings.py

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    #DRF
    'rest_framework',
    'rest_framework.authtoken',
    
    # rest_auth
    'rest_auth'

    #My Apps
    'accounts',
    'articles',
]

# DRF auth settings
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
				# 우리는 토큰값을 쓸 것이기 때문에!
        'rest_framework.authentication.TokenAuthentication',
    ]
}
```

- urls.py

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', include('articles.urls'))

    # rest-auth
    path('rest-auth/', include('rest_auth.urls'))
]
```

- rest-auth라는 `앱`이 붙었다! → migrate가 필요함
- 슈퍼유저 하나 만듦

### 로그인

- /rest-auth/login/

<img width="1392" alt="_2020-08-27__8 05 18" src="https://user-images.githubusercontent.com/60081217/91643211-25d05800-ea6c-11ea-98d3-19f738b1851d.png">

### 회원가입

<img width="1392" alt="_2020-08-27__8 05 18" src="https://user-images.githubusercontent.com/60081217/91643208-249f2b00-ea6c-11ea-96e0-70c0065f635d.png">

- settings.py

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # registration
    'django.contrib.sites',

    #DRF
    'rest_framework',
    'rest_framework.authtoken',
    
    # rest_auth + all_auth
    'rest_auth',
    'allauth',
    'allauth.account',
    'rest_auth.registration',

    #My Apps
    'accounts',
    'articles',
]

# django sites app setting
SITE_ID = 1
```

- urls.py

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', include('articles.urls')),

    # rest-auth
    path('rest-auth/', include('rest_auth.urls')),
    path('rest-auth/signup/', include('rest_auth.registration.urls'))
]
```

<img width="1392" alt="_2020-08-27__8 23 28" src="https://user-images.githubusercontent.com/60081217/91643214-2963df00-ea6c-11ea-94a8-4465a60cd720.png">

### 새글 쓰기

- user 정보가 필요함 → user에도 serializer가 필요함
- accounts/serializers.py

```python
from django.contrib.auth import get_user_model
from rest_framework import serializers

User = get_user_model()

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        moder = User
        fields = ['id', 'username']
```

- articles/sericalizers.py

```python
from rest_framework import serializers
from accounts.serializers import UserSerializer
from .models import Article

class ArticleListSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'created_at']

class ArticleSerializer(serializers.ModelSerializer):
    # 아티클을 serializer를 할때 user라는 필드가 있는데 , 그 필드는 전부 userserializer가 따로 전해져 온다.
    user = UserSerializer(required=False) # is_valid() 에서 유무 검증을 pass한다.
    class Meta:
        model = Article
        fields = '__all__'
```

- 하지만 아직도 에러가 난다 → user정보를 어디서 가져오지?
- views.py

```python
from django.shortcuts import get_object_or_404
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response

from .serializers import ArticleListSerializer, ArticleSerializer
from .models import Article

@api_view(['POST'])
@permission_classes([IsAuthenticated]) # 인증된 사용자인지 확인한다.
def create_article(request):
    serializer = ArticleSerializer(data=request.data)
    # raise_exception은 if문을 통과하지 못한 애들한테 뭐가 잘못됐는지 말해줌
    if serializer.is_valid(raise_exception=True):
        serializer.save(user=request.user)
        return Response(serializer.data)
```

<img width="1392" alt="_2020-08-27__8 44 42" src="https://user-images.githubusercontent.com/60081217/91643216-2b2da280-ea6c-11ea-9cc7-94b308fae7a0.png">

- Heade에 토큰값 같이 넣어준다.

### 로그아웃

- /rest-auth/logout/
- 로그아웃의 핵심은 : token destroy
- 근데 현재 좀 로그아웃 이슈가 있긴 함..

<img width="1392" alt="_2020-08-27__8 52 11" src="https://user-images.githubusercontent.com/60081217/91643217-2d8ffc80-ea6c-11ea-80b3-1c2f71d2d5b2.png">