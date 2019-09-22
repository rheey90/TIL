# scrapy-djangoitem

해당 라이브러리는 존재하는 장고 모델로부터 스크래피 아이템을 정의하는 확장 프로그램이다. 이 유틸리티는 Djangoitem이라는 새로운 객체를 소개하며 이는 기존 스크래피 아이템처럼 사용할 수 있고 장고 모델 속성을 지니고 있어 장고 모델로 바로 연결하여 쓸 수 있다. 즉, Djangoitem은 장고 모델로부터 필드 정의를 가져오고 연결성을 지을 수 있다.

Djangoitem은 장고의 ModelForms와 유사하게 작동되는데 먼저 하위 객체를 생성하여 유효한 장고 모델으로 만들기 위해 속성을 정의할 수 있다. 또한, 기존 장고 모델의 필드를 오버라이드나 추가할 수 있다.

```python
from django.db import models # 장고 모델 생성

class Person(models.Model):
	name: models.CharField(max_length=255)
	age = models.IntegerField()
```

```python
from scrapy_djangoitem import DjangoItem # 장고 아이템 정의

class PersonItem(DjangoItem):
	django_model = Person
  
p = PersonItem() # 스크래피 아이템과 작동 방식이 동일
p['name'] = 'John'
p['age'] = '22'

person = p.save() # DjangoItem.save()란 메소드를 사용하여 person이라는 변수에 객체를 저장한다는 뜻으로 선언
print(person.name) --> 'John'
print(person.age) --> '22'
print(person.id) --> 1
```

Djangoitem은 장고와 스크래피 프로젝트를 연결하는데 있어 편리성을 제공하지만 많은 내용을 크롤링한다고 장고 ORM과 잘 맞지 않는다는 단점을 지니고 있다.



### 실제 적용

Rotten Tomatoes로부터 2019년에 나온 톱 100개 영화를 크롤링하는 예제이다.

```
$ python manage.py startapp movie // 장고 앱 생성
```

```python
from django.db import models // 장고 모델 생성


class Movie(models.Model):
    title = models.CharField('Título',max_length=255, unique=True)
    critics_consensus = models.TextField('Consenso da crítica', blank=True, null=True)
    average_grade = models.DecimalField('Nota média', max_digits=3, decimal_places=2, blank=True, null=True)
    poster = models.ImageField('Capa', blank=True, null=True)
    amount_reviews = models.PositiveIntegerField('Quantidade de criticas', blank=True, null=True)
    approval_percentage = models.PositiveIntegerField('Porcentagem de aprovação', blank=True, null=True)

    def __str__(self):
        return self.title
```

```python
from django.contrib import admin # 장고 admin 생성
from .models import Movie

class MovieAdmin(admin.ModelAdmin):
    pass
admin.site.register(Movie, MovieAdmin)
```

```python
INSTALLED_APPS = [
    ...
    'django.contrib.staticfiles',
    'movie',
]
MEDIA_URL = '/media/' # 영화 포스터 저장
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

```
$ pip install Pillow
```

```python
urlpatterns = [
    url(r'^admin/', admin.site.urls),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

```
$ python manage.py makemigrations && python manage.py migrate
$ python manage.py createsuperuser
```

```
$ pip install scrapy==1.5 # 스크래피 설치
$ pip install scrapy-djangoitem==1.1.1 # 스크래피 장고아이템 설치
$ scrapy startproject crawling
```

```python
crawler/items.py
import scrapy # 위에 만든 장고 모델을 가져와 장고 아이템을 정의
from scrapy_djangoitem import DjangoItem
from movie.models import Movie

class MovieItem(DjangoItem):
    django_model = Movie
    image_urls = scrapy.Field()
    images = scrapy.Field()
```

```python
crawler/settings.py
import os
import sys
sys.path.append(os.path.join(os.path.dirname(os.path.dirname(os.path.abspath(__file__))), ".."))
os.environ['DJANGO_SETTINGS_MODULE'] = 'best_movies.settings'
import django
django.setup()
DOWNLOAD_DELAY = 3
```

```python
from best_movies.settings import MEDIA_ROOT # 영화 포스터 저장
IMAGES_STORE = MEDIA_ROOT
ITEM_PIPELINES = {
    'scrapy.pipelines.images.ImagesPipeline': 100,
    'crawling.pipelines.CrawlingPipeline': 100,
}
```

```python
import scrapy
from crawling.items import MovieItem
from scrapy.spiders import CrawlSpider

# 스파이더 생성
class RottenTomatoesSpider(CrawlSpider):

    name = 'rottentomatoes'
    allowed_domains = ['rottentomatoes.com']
    start_urls = ['https://www.rottentomatoes.com/top/bestofrt/?year=2018',]

    def parse(self, response):
        rows = response.xpath('//*[@class="table"]/tr/td[3]/a/@href').extract()
        for row in rows:
            link = 'https://www.rottentomatoes.com' + row
            yield scrapy.Request(url=link, callback=self.parse_item)

    def parse_item(self, response):
        i = MovieItem()
        i['title'] = response.css('h1.mop-ratings-wrap__title ::text').extract_first()
        i['critics_consensus'] = response.css('p.mop-ratings-wrap__text--concensus ::text').extract()
        i['average_grade'] = response.css('#js-rotten-count ::text').extract()[1]
        i['amount_reviews'] = response.css('.mop-ratings-wrap__text--small ::text').extract()[1]
        i['approval_percentage'] = response.css('.mop-ratings-wrap__percentage ::text').extract_first()
        i['image_urls'] = response.css('.posterImage ::attr(data-src)').extract()
        return i
```

```python
from movie.models import Movie #데이터 정리

def clean_title(param):
    return param

def clean_critics_consensus(param):
    return ' '.join(param)

def clean_average_grade(param):
    param = param.strip().replace('/5', '')
    return param

def clean_poster(param):
    if param:
        param = param[0]['path']
    return param

def clean_amount_reviews(param):
    return param.strip()

def clean_approval_percentage(param):
    return param.strip().replace('%', '')


class CrawlingPipeline(object):
    def process_item(self, item, spider):
        title = clean_title(item['title'])
        critics_consensus = clean_critics_consensus(item['critics_consensus'])
        average_grade = clean_average_grade(item['average_grade'])
        poster = clean_poster(item['images'])
        amount_reviews = clean_amount_reviews(item['amount_reviews'])
        approval_percentage = clean_approval_percentage(item['approval_percentage'])

        Movie.objects.create(
            title=title,
            critics_consensus=critics_consensus,
            average_grade=average_grade,
            poster=poster,
            amount_reviews=amount_reviews,
            approval_percentage=approval_percentage,
        )

        return item
```



### Reference

https://github.com/scrapy-plugins/scrapy-djangoitem

https://medium.com/@tiago.piovesan.tp/make-a-crawler-with-django-and-scrapy-a41adfdd24d9