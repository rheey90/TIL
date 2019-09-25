# 4주 프로젝트 9일차 (2019/09/25)

트위치에서 가져온 유저와 동영상 정보를 데이터베이스로 옮기는 작업을 했다. 먼저 selenium과 트위치 API로 가져온 JSON형 데이터에서 필요한 정보를 뽑아 변수로 옮긴 후 데이터베이스로 옮겼다. 데이터베이스를 mysql을 쓴다면 pymysql 라이브러리를 사용해 쉽게 퀴리문을 사용하여 데이터베이스를 조작할 수 있다.

```python
# 트위치 유저 정보를 데이터베이스로 옮기기
import pymysql.cursors
import requests
import time
from selenium import webdriver
from bs4 import BeautifulSoup

headers = {'Client-ID': 'orr8549md8anh4puxs904dyswcgfb3'}

conn = pymysql.connect(host='localhost', user='root',
                       password=None, db='ilio', charset='utf8mb4') // 데이터베이스를 연결. mysql에 미리 만들어 있어져야 한다.
user_name = 'overwatchleague'
user_id = '137512364'

try:
    options = webdriver.ChromeOptions()
    options.add_argument('headless')
    driver = webdriver.Chrome(
        '/Users/damagedcase/Documents/Code States/IM14/crawling_practice/chromedriver', options=options)
    driver.get('https://www.twitch.tv/' + user_name)
    time.sleep(5)
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    spans = soup.find_all(
        'span', class_='tw-font-size-5')
    spans_array = []
    for i in spans:
        spans_array.append(i.text.strip())
    subscribers_count = spans_array[5]
    r = requests.get('https://api.twitch.tv/helix/users?id=' +
                     user_id + '', headers=headers)
    user = r.json()
    image_url = user['data'][0]['profile_image_url']
    user_name = user['data'][0]['display_name']
    user_info = user['data'][0]['description']

    with conn.cursor() as cursor:
        sql = 'INSERT INTO myapi_users (image_url, user_name, signup_date, user_info, subscribers_count) VALUES (%s, %s, %s, %s, %s)' // 쿼리 문. %s는 placeholder
        cursor.execute(sql, (image_url, user_name, 'null',
                             user_info, subscribers_count))
    conn.commit()
finally:
    driver.close()
    conn.close()

# 트위치 동영상을 데이터베이스로 옮기기. 아까 전과 동일한 방법으로.
import pymysql.cursors
import requests

headers = {'Client-ID': 'orr8549md8anh4puxs904dyswcgfb3'}

conn = pymysql.connect(host='localhost', user='root',
                       password=None, db='ilio', charset='utf8mb4')
user_name = 'overwatchleague'
user_id = '137512364'

try:
    r = requests.get(
        'https://api.twitch.tv/helix/videos?user_id=' + user_id + '', headers=headers)
    videos = r.json()
    for video in videos['data']:
        title = video['title']
        update_data = video['published_at']
        view_count = video['view_count']
        platform = 'twitch'
        with conn.cursor() as cursor:
            sql = 'INSERT INTO myapi_videos (title, update_date, view_count, like_count, comments_count, platform) VALUES (%s, %s, %s, %s, %s, %s)'
            cursor.execute(sql, (title, update_data, view_count,
                                 'null', 'null', platform))
        conn.commit()
finally:
    conn.close()


```

Fake data 만드는 작업을 했는데 https://mockaroo.com/에서 만들면 무척 간단하고 JSON 형태로도 저장할 수 있어 좋다.

