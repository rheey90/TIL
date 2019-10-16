# 4주 프로젝트 3일차 (2019/09/18)

트위치 스트리머 팔로워 수 이 외에 기업에서 요청한 다른 데이터들을 크롤링 해보왔다. 다행히 다른 데이터들은 selenium과 bs4 라이브러리를 사용할 필요 없이 트위치 API를 사용해서 구했다. 먼저 트위치 API를 활용하려면 트위치 개발자 사이트로 가서 로그인 한 다음 API를 활용할 앱에 대해서 간단한 정보를 입력한 후 클라이언트 아이디를 부여 받아 이를 헤더에 세팅해서 요청을 날려야 한다.

### requests

파이썬으로 API 활용은 requests 라이브러리를 사용하여 할 수 있다. 이 과정은 자바스크립트에서 express 쓰는 것과 흡사하다. 예를 들어 스트리머 정보를 가져오기 위해서 다음과 같은 코드를 실행하였다.

```python
import requests

headers = {'Client-ID': '<your client ID>'}

def get_user():
    r = requests.get(
        'https://api.twitch.tv/helix/users?id=138896088', headers=headers)
    print(r.json())

```

요청이 제대로 처리되면 json 형태로 출력되도록 하였다. 생각보다 많이 간단해서 스트리머 정보 이 외에 다른 데이터들을 쉽게 가져올 수 있었다. 다만, 한 가지의 약간 까다로운 점이 있다면 모든 API 요청이 유저 네임을 받지 않아 유저 아이디를 써야하는데 유저 아이디는 트위치 웹 페이지에서 직접 찾을 수 없음으로 크롬 확장 프로그램을 사용하여 찾아야 한다.

다음은 스트리머 정보를 얻기 위한 객체를 만들었다.

```python
import requests
from selenium import webdriver
from bs4 import BeautifulSoup

headers = {'Client-ID': '<your client ID>'}


class Streamer_info:
    def __init__(self, user_name, user_id):
        self.user_name = user_name
        self.user_id = user_id

    def get_number_of_followers(self):
        try:
            options = webdriver.ChromeOptions()
            options.add_argument('headless')
            driver = webdriver.Chrome(
                '/Users/damagedcase/Documents/Code States/IM14/crawling_practice/chromedriver', options=options)
            driver.implicitly_wait(20)
            driver.get('https://www.twitch.tv/' + self.user_name)
            html = driver.page_source
            soup = BeautifulSoup(html, 'html.parser')
            spans = soup.find_all(
                'span', class_='tw-font-size-5')
            spans_array = []
            for i in spans:
                spans_array.append(i.text.strip())
            number_of_followers = spans_array[5]
            print("Number of followers: ", number_of_followers)
        finally:
            driver.close()

    def get_user(self):
        r = requests.get('https://api.twitch.tv/helix/users?id=' +
                         self.user_id + '', headers=headers)
        print('User info: ', r.json())

    def get_videos(self):
        r = requests.get(
            'https://api.twitch.tv/helix/videos?user_id=' + self.user_id + '', headers=headers)
        print('User videos: ', r.json())

    def get_stream(self):
        r = requests.get(
            'https://api.twitch.tv/helix/streams?user_id=' + self.user_id + '', headers=headers)
        print('User stream: ', r.json())
```

특정 스트리머의 정보를 얻으려면 Streamer_info 객체의 인스턴스를 만들어주어 메소드들을 실행하면 얻을 수 있다. API로 구현한 메소드들은 원활하게 실행되지만 팔로워 수를 찾아오는 메소드 좀 불안정한데 그 이유는 아직 찾지 못 했다.