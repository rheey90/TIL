# 4주 프로젝트 2일차 (2019/09/17)

오늘은 트위치 스트리머의 팔로워 수를 크롤링하는 작업을 하였다. 파이썬을 사용해 웹 크롤링할 수 있는 라이브러리는 bs4와 scrapy가 있는데 bs4가 scrapy보다 사용하기 더 쉬워보여 선택했다.



### bs4

```
$ pipenv shell // 파이썬 가상 환경 설정
$ pipenv requests // 가상 환경에 requests 라이브러리 설치
$ pipenv install bs4 // 가상 환경에 bs4 라이브러리 설치
```

먼저 해당 유저의 팔로워 수를 크롤하기 위해 유저 페이지로 들어가 개발자 콘솔 창을 열어 찾고자 하는 태그를 찾았다.

![스크린샷 2019-09-17 오후 9.37.35](/Users/damagedcase/Documents/TIL/four-week-project/스크린샷 2019-09-17 오후 9.37.35.png)

```python
import requests
from bs4 import BeautifulSoup
# 필요한 라이브러리 가져오기

url = 'https://www.twitch.tv/overwatchleague/' # 크롤할 웹 페이지
html = requests.get(url) // HTTP GET 요청
soup = BeautifulSoup(html.text, 'html.parser')
spans = soup.find_all('span', class_='tw-font-size-5')
print(spans)
```

팔로워 수는 span 테그의 tw-font-size-5 클래스를 갖고 있고 id가 없으므로 해당 클래스를 가진 span 테그를 배열로 가져와 팔로워 수가 있는 인덱스를 찾아 따로 변수로 할당하려고 했다. 그러나, 비동기로 처리가 되어 웹 페이지가 다 로딩이 안 된 상태로 크롤이 되어 결과 값이 빈 배열로 계속 나왔다.



### selenium

위의 문제를 해결하기 위해 selenium 라이브러리를 사용했다. selenium은 웹앱을 테스트 하는데 이용되고 webdriver라는 API를 통해 크롬 브라우저를 제어할 수 있다.

```
$ pipenv install selenium
```

```python
from selenium import webdriver
from bs4 import BeautifulSoup

driver = webdriver.Chrome(
    '/Users/damagedcase/Documents/Code States/IM14/crawling_practice/chromedriver')
driver.implicitly_wait(15) #15초 동안 암묵적으로 웹 페이지가 로드되도록 기다림
driver.get('https://www.twitch.tv/overwatchleague/')

html = driver.page_source
soup = BeautifulSoup(html, 'html.parser')
soup.prettify()
spans = soup.find_all(
    'span', class_='tw-font-size-5')
spans_array = []
for i in spans:
    spans_array.append(i.text.strip()) # span 테그를 처리하고 텍스트만 보여줌
number_of_followers = spans_array[5]
print(number_of_followers)

```

driver라는 webdriver 인스턴스를 만들고 웹 페이지의 모든 자원이 로드될 때까지 암묵적으로 기다라는 코드를 삽입하였다. 그렇게 처리한 후 bs4를 사용해서 기존에 하려던 작업을 무사히 마칠 수 있었다.



### 트위치 스트리머 지표

- 팔로워 수
- 동영상 뷰
- 스트림 시청자 수