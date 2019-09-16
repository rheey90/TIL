# Django

Django (장고)는 파이썬으로 쓰여진 웹 프레임워크로서 빠르고 보안이 철저하다는 특징을 지니고 있다. 장고 프로젝트는 각각의 다른 기능을 지닌 앱들의 집합체로 볼 수 있다. 클라이언트 측에서 HTTP 요청에 따라 동적으로 해당 URL로 이어주고 요청을 처리할 수 있다. 템플릿을 사용하여 데이터베이스로부터 요청된 데이터를 처리하여 HTML 페이지에 렌더해서 보여줄 수 있다. 즉, 장고 프로젝트는 종합적으로 프론트엔드와 백엔드 작업을 가능캐하는 웹 프레임워크이다



### 장고 설치

장고 프로젝트를 실행하기 위해서 가상 환경을 구축하는 것을 주로 권장된다. 이는 새로운 소프트웨어나 앱이 로컬 시스템에 어떤 영향을 끼칠지 모르는 불확실성 때문이다.

```
$ pip install pipenv //pipenv란 가상 환경을 설치
$ pipenv shell //pipenv 가상 환경 실행
$ pipenv install django// 가상 환경에 장고를 설치
```



### 프로젝트 실행

```
$ django-admin startproject mysite
```

프로젝트를 시작하기 위해서 위와 같은 명령어를 터미널에서 실행한다. 위의 명령어의 실행이 완료되면 프로젝트 명과 일치하는 폴더와 manage.py라는 파일이 생성된다.

mysite 폴더에 보면 4개의 파일들이 생성된 것을 볼 수 있다.

1. __  init  __.py 는 빈 파일이고 파이썬이 한 파이썬 페키지라는 것을 인식하게 만들어준다.
2. settings.py 파일에는 프로젝트 설정이 저장된다.
3. urls.py 파일에는 URL 패턴들이 저장되어 있으며 앱의 다른 페이지들로 이어준다.
4. wsgi.py 파일은 웹 서버의 게이트웨이 인터페이스로 작동한다.

```
$ python manage.py runserver
```

위의 명령어를 실행하면 서버가 작동되면 http://127.0.0.1:8000/ 로 브라우저에서 접근을 하면 로컬에서 돌아가는 가장 첫 번째 웹 페이지를 볼 수 있게 된다.



### 새로운 앱을 구현하기

```
$ python manage.py startapp my_app
```

위와 같은 명령어를 실행하여 새로운 앱 만들기를 시작할 수 있고 다음과 같은 파일들이 생성된다.

1. __ init __.py 빈 파일로 파이썬이 해당 폴더가 페키지로 인식할 수 있게끔 한다.
2. admin.py 장고 admin 인터페이스로써 모델들을 등록할 수 있게 해준다.
3. app.py 앱의 특정 설정들을 넣을 수 있다.
4. models.py 데이터베이스에 들어갈 모델들과 모델 간 관계를 설정할 수 있다. 자바스크립트에서 Sequelize의 테이블 설정과 비슷하다고 볼 수 있다.
5. test.py 엡이 잘 구동되는지에 대한 테스트를 작성하는 파일이다.
6. views.py 요청과 응답을 처리한다.
7. migrations 데이터베이스에 모델과 관련된 데이터를 저장하는데 쓰인다. Sequelize CLI의 migration과 비슷하다고 볼 수 있다.



### 뷰 구현하기

```python
from django.http import HttpResponse
```

views.py에서 뷰를 구현하고 urls.py 파일에다 맵핑하기 위해서 먼저 필요한 것들을 import 해와야 한다.

```python
def index(request):
	return HttpResponse("Hello World")
```

요청이 들어오면 HTTP 응답으로서 Hello World를 출력해주고 my_app 폴더에 있는 urls.py 파일에서는

```python
from django.urls import path
from . import view

urlpatterns = [
	path('', views.index, name = "index"),
]
```

그리고 mysite 폴더에 있는 urls.py 파일에 다음과 같이 코드를 작성한다.

```python
from django.contrib import admin
from django.urls import path
from django.urls import include

urlpatterns = [
	path('my_app', views.index, name = "index"),
  path('admin/', admin.site.urls),
]
```

뷰와 뷰를 맵핑할 URL 패턴을 짤 수 있다. 이제 http://127.0.0.1:8000/my_app 을 접속하면 Hello World가 구현되어 있는 것을 볼 수 있다.



### 모델

Models를 사용하여 데이터베이스에 데이터를 저장할 수 있다. 각 모델은 데이터베이스의 테이블로 맵핑이 된다.

```
$ python manage.py migrate
```

위와 같은 명령어를 터미널에 실행하면 테이블을 데이터베이스에 생성할 수 있다.

```python
from django.db import models

class Question(models.Model):
	question_text = models.CharField(max_length=200)
	pub_date = models.DateTimeField('date published')
	
	def __str__(self):
		return self.question_text

class Choice(models.Model):
	question = models.ForeignKey(Question, on_delete=models.CASCADE)
	choice_text = models.CharField(max_length=200)
	votes = models.IntegerField(default=0)
	
	def __str__(self):
		return self.choice_text
```

모델 만드는 방식은 Sequelize과 유사한데 Sequelize는 객체 형태를 지닌 반면 장고에서는 class를 쓴다는 점에서 다르다. 그리고 ForeignKey를 사용하여 두 모형의 관계를 맺을 수 있다.

```
$ python manage.py makemigrations polls
```

위와 같은 명령어를 터미널에서 실행하면 장고한테 새로운 모델을 만들었거나 이미 존재하는 모델에 변경이 있었음을 알릴 수 있다.

- SQL에 CREATE TABLE과 같은 동작이 실행된다.
- 데이터베이스에 생성된 테이블들을 접근할 수 있는 API가 생성된다.

```
$ python manage.py migrate //데이터베이스에 모델에 따른 테이블들을 생성
```

```python
$ python manage.py shell
>>> from polls.models import Choice, Question # 모델들을 import
>>> from django.utils import timezone
>>> Question.objects.all() # 모든 데이터를 가져옴
>>> q = Question(question_text="What's new?", pub_date=timezone.now()) # 모델에 새로운 인스턴스를 생성하여 삽입될 데이터를 키와 값의 형태로 입력
>>> q.save() # 저장
>>> q.id
>>> q.question_text
>>> q.pub_date
>>> q.question_text = "What's up?" # q의 question_text를 변경
>>> q2 = Question(question_text="What's new?", pub_date=timezone.now())
>>> q2.save()
>>> Question.objects.all()
<QuerySet [<Question: What's up?>, <Question: What's new?>]>
```

```python
>>> q.choice_set.create(choice_text="Not much", votes=0)
<Choice: Not much>
>>>  q.choice_set.create(choice_text="The sky", votes=0)
<Choice: The sky>
```



### 관리자 기능

장고에서는 따로 관리자 페이지를 생성할 필요없이 자동으로 관리자 인터페이스 모델을 만들어준다.

```
$ python manage.py createsuperuser // 새로운 관리자 유저 생성
```

http://127.0.0.1:8000/admin 으로 접속하면 관리자 계정을 접근할 수 있다.

```python
from django.contrib import admin
from .models import Question
admin.site.register(Question)
```

polls 폴더 안에 있는 admin.py 파일에 위와 같은 코드를 작성하여 Question 모델을 관리자 페이지에서 추가 혹은 삭제할 수 있는 기능이 생기게 된다.



### 템플릿

장고에서 템플릿은 파이썬으로 HTML의 DOM 요소들을 조작할 때 쓰인다.

1. {{ variable }}: 변수
2. {% logic %} 로직, 반복문, 링크

```python
from django.shortcuts import get_object_or404, render
from django.http import HttpResponse
from . models import Question

def index(request):
	latest_question_list = Question.objects.order_by('-pub)date')[:5] # 날짜 기준으로 첫 5개의 데이터를 출력
	context = {'latest_question_list': latest_question_list}
	return render(request, 'polls/index.html', context)

def detail(request, question_id):
	question = get_object_or_404(Question, pk=question_id)
	return render(request, 'polls/detail.html', {'question': question})
	
def results(request, question_id):
	response = "You're looking at the results of question %s."
	return HttpResponse(response % question_id)
	
def vote(request, question_id):
	return HttpResponse("You're voting on question %s." % question_id)
```

template/polls/views.py에 위와 같은 코드를 작성하여 index, detail, results, vote 뷰를 만듬.

```python
from django.contrib import admin
from django.urls import path
from . import views

app_name = 'polls'
urlpatterns = [
	path('', views.index, name='index'),
	path('<int:question_id>/', views.detail, name='detail'),
	path('<int:question_id>/results/', views.results, name='results'),
	path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

polls/urls.py URL 패턴을 작성. 자바스크립트 MVC 모형에서 router와 흡사.

```HTML
{% if latest_question_list %}
	<ul>
	{% for question in latest_question_list %}
		<li><a href="{% url 'polls:detail' question.id %}">{{question.question_text}}</a></li>
	{% endfor %}
{% else %}
	<p>No polls are available.</p>
{% endif %}
```

polls/index.html에 위와 같이 HTML에 뿌려질 내용을 작성.

```python
# polls/detail.py
{{ question }}
```



### Reference

https://medium.com/datadriveninvestor/python-django-views-templates-models-f0844a00db70