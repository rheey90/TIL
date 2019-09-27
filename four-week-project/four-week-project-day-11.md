# 4주 프로젝트 11일차 (2019/09.27)

multiprocessing 라이브러리를 이용하여 다중 프로세스와 쓰레드를 사용하여 selenium과 bs4로 트위치 스트리머의 팔로워 수를 크롤링 해봤다.

```python
from multiprocessing import Pool
import time
from selenium import webdriver
from bs4 import BeautifulSoup


def get_followers(user_name):
    try:
        options = webdriver.ChromeOptions()
        options.add_argument('headless')
        driver = webdriver.Chrome(
            '/Users/damagedcase/Documents/Code States/IM14/crawling_practice/chromedriver', options=options)
        driver.get('https://www.twitch.tv/' + user_name + '/followers')
        time.sleep(10)
        html = driver.page_source
        soup = BeautifulSoup(html, 'html.parser')
        h2 = soup.find(
            'h2', class_='tw-c-text-alt-2')
        number_of_followers = h2.text.strip()
        print("Number of followers:", number_of_followers)
    finally:
        driver.close()


user_names = ['overwatchleague', 'drlee_kor', 'bighead033', 'sonycast_',
              'wpghd321', 'buzzbean11', 'miro_geng', 'mirage720', 'firebather', 'rudbeckia7']

if __name__ == '__main__':
    start_time = time.time()
    pool = Pool(2) 
    pool.map(get_followers, user_names)
    pool.close()
    pool.join()
    print('(2 processes) Seconds:', (time.time() - start_time))

```

Pool이 사용할 프로세서 개수이다. 컴퓨터 성능에 따라서 다중 브라우저 창을 켤 시, 웹 페이지가 다 로딩이 안 됐을 경우 크롤링이 안 될 수 있으므로 time.sleep() 메소드 사용하는 것이 좋다.

```python
from multiprocessing.dummy import Pool as ThreadPool
import time
from selenium import webdriver
from bs4 import BeautifulSoup


def get_followers(user_name):
    try:
        options = webdriver.ChromeOptions()
        options.add_argument('headless')
        driver = webdriver.Chrome(
            '/Users/damagedcase/Documents/Code States/IM14/crawling_practice/chromedriver', options=options)
        driver.get('https://www.twitch.tv/' + user_name + '/followers')
        time.sleep(10)
        html = driver.page_source
        soup = BeautifulSoup(html, 'html.parser')
        h2 = soup.find(
            'h2', class_='tw-c-text-alt-2')
        number_of_followers = h2.text.strip()
        print("Number of followers:", number_of_followers)
    finally:
        driver.close()


user_names = ['overwatchleague', 'drlee_kor', 'bighead033', 'sonycast_',
              'wpghd321', 'buzzbean11', 'miro_geng', 'mirage720', 'firebather', 'rudbeckia7']

if __name__ == '__main__':
    start_time = time.time()
    pool = ThreadPool(8)
    pool.map(get_followers, user_names)
    pool.close()
    pool.join()
    print('(8 threads) Seconds:', (time.time() - start_time))

```

멀티 쓰레드 사용할 시, multiprocessing을 불러올 때 .dummy를 붙여주면 된다. 나머지는 멀티 프로세스를 사용할 때와 동일하다. 확실히 쓰레드나 프로세스를 많이 쓰면 처리 속도가 빨라지나 어느 정도의 개수를 넘으면 가장 빠른 시간을 보여준 개수보다 조금 더 느려졌다. 개수를 늘리면서 가장 빠른 개수를 찾는 것이 중요한거 같다.