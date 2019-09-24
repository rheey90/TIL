# 4주 프로젝트 8일차 (2019/09/24)

### REST API

REST API는 다른 앱에 데이터를 제공하는 표준적인 방법으로써 앱들이 데이터를 쉽게 사용할 수 있도록 돕는다. 이는 5가지 방법으로 데이터를 수신하고 조작할 수 있게끔 해준다.

1. GET - 엔드포인트와 파라미터에 따라 API가 그에 해당하는 데이터를 반환함.
2. POST  - 데이터베이스에 새로운 데이터를 삽입.
3. PUT - 주어진 URI에 요청하는 데이터가 존재하면 그 데이터를 수정함.
4. DELETE - 주어진 URI에 있는 데이터를 삭제.
5. PATCH - 개별 필드 내 기록을 업데이트

API는 데이터베이스의 창문으로 비유될 수 있으며 API 백엔드는 데이터베이스로 오는 쿼리와 응답을 포멧하는 역할을 수행한다. 주로 JSON 형태로 데이터를 수신 및 송신하는데 이러한 프로세스를 serialization이라 부른다.



###Django REST Framework

![alt](https://miro.medium.com/max/2000/1*lAMsvtB6afHwTQYCNM1xvw.png)

Django REST Framework를 사용하는 이유는 serialization을 아주 간단하게 처리하기 때문이다. 장고에서는 파이썬으로 데이터베이스 모델을 만들 수 있고 장고 ORM이 데이터베이스와 쿼리를 처리해준다. 여기에 Django REST Framework는 거기에 얹어 데이터베이스 모델을 RESTful 포멧에 맞춰 serialize를 가능캐 해준다.



### 장고 셋업

```
$ pyenv virtualenv django-rest // 가상환경 설정
$ pyenv local django-rest
```

```
$ pip install django // 장고 설치
$ django-admin startproject mysite // 장고 프로젝트 실행
$ cd mysite
$ python manage.py runserver // 서버가 실행되는지 체크
$ python manage.py startapp myapi // API 앱 생성
```

```python
# mysite/settings.py

INSTALLED_APPS = [
	'myapi.apps.MyapiConfig',
	...
]
```

mysite/settings.py에 myapi란 새로운 앱을 만들었다는 알려주기 위해 써준다.

```
$ python manage.py migrate // 데이터베이스를 migrate
```

```
$ python manage.py createsuperuser // admin 계정 생성

Username (leave blank to use 'bennet'):
Email address: < your email >
Password:
Password (again):
Superuser created successfully.
```



### 데이터베이스 생성

```python
# myapi/models.py
# 데이터베이스 테이블 생성

from django.db import models

class Hero(models.Model):
	name = models.CharField(max_length=60)
	alias = models.CharField(max_length=60)
	
	def __str__(self):
		return self.name
```

```
$ python manage.py makemigrations // 데이터베이스 테이블을 migrate
$ python manage.py migrate
```

```python
# myapi/admin.py

from django.contrib import admin
from .models import Hero

admin.site.register(Hero)
```

admin 엔드포인트에서 Hero 모델의 존재여부를 모르기 때문에 위의 코드와 같이 admin 사이트에 등록을 해야한다. 그리고 localhost:8000/admin에 접속하면 MYAPI 섹션에 Hero가 생긴 것을 볼 수 있다. Hero란에 'Add' 버튼을 누르면 직접 name과 alias를 입력하여 데이터베이스에 추가할 수 있다.



### Django REST Framework 셋업

```
$ pip install djangorestframework // 프레임워크 설치
```

```python
# mysite/settings.py
# myapi를 생성했을 때와 마찬가지로 새로운 프레임워크가 생겼다는 것을 알려야 함

INSTALLED_APPS = [
	# All your installed apps stay the same
	...
	'rest_framework',
]
```



### Hero 모델을 serialize하기

Hero 모델을 serialize하기 위해서 myapi 폴더에 serializers.py 파일을 생성한다.

```python
from rest_framework import serializers # rest_framework에서 serializers를 import
from .models import Hero # models 파일에서 Hero 클래스를 import

# Hero 클래스를 자신의 serializer에 연결하는 객체를 생성
class HeroSerializer(serializers.HyperlinkedModelSerializer):
	class Meta:
		model = Hero
		fields = ('name', 'alias')
```



### 데이터 보기

각 hero에 대한 데이터를 JSON 형태로 렌더하기 위해서 먼저 데이터베이스에 모든 hero에 대한 쿼리를 보내고 받은 퀘리 세트를 serializer 사용하여 JSON 형태로 변환하고 렌더해야 한다.

```python
# myapi/views.py

from rest_framework import viewsets

from .serializers import HeroSerializer
from .models import Hero

class HeroViewSet(viewsets.ModelViewSet):
	queryset = Hero.objects.all().order_by('name')
	seriralizer_class = HeroSerializer
```

다음은 mysite 폴더와 myapi 폴더에 있는 urls.py 파일을 열어 API에 대한 URL을 추가해주면 된다.

```python
# mysite/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
	path('admin/', admin.site.urls),
	path('', include('myapi.urls')),
]
```

```python
# myapi/urls.py
from django.urls import include, path
from rest_framework import routers
from . import views

router = routers.DefaultRouter()
router.register(r'heroes', views.HeroViewSet)

# Wire up our API using automatic URL routing.
# Additionally, we include login URLs for the browsable API.
urlpatterns = [
    path('', include(router.urls)),
    path('api-auth/', include('rest_framework.urls', namespace='rest_framework')),
]
```

Django REST Framework는 Node.js의 express 모듈처럼 라이터를 제공하고 데이터베이스에 데이터를 추가하거나 삭제하면 요청에 맞춰 URL 자동으로 업데이트 해주어 처리한다.

![alt](https://miro.medium.com/max/1475/1*5faTw-2H3KSl1TLEwIu-pw.png)

http://localhost:8000로 들어가면 위와 같은 화면이 뜬다. 다음, http://localhost:8000/heroes로 들어가면 자동으로 GET 요청이 실행되어 데이터베이스에 들어가있는 모든 hero들에 대한 정보가 출력된다.

![alt](https://miro.medium.com/max/1469/1*qMRP7Mk-RNtxvCVyrUMP3A.png)

동일한 페이지에서 하단으로 내려가면 application/json 옵션으로 POST 요청을 날릴 수도 있다.

![alt](https://miro.medium.com/max/1499/1*o-GCCof8s_4Qot95Dhst2Q.png)



###Reference

https://medium.com/@BennettGarner/build-your-first-rest-api-with-django-rest-framework-e394e39a482c