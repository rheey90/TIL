# 4주 프로젝트 13일차 (2019/09/30)

오늘은 데몬 프로세스를 만들어 보았다. 처음에는 파이썬에 multiprocessing에 데몬이 있어 데몬 옵션을 불리언 값으로 설정하여 돌려보려 했지만 공식 문서를 보니 유닉스와는 다른 개념의 데몬이라 다른 방법을 알아보았다. 그 중에 크론이라는 데몬 프로세스에 대해서 알게되었는데 이는 시간 설정에 따라 특정 프로그램을 사용하게 해주어 적합하다고 판단되어 이를 쓰려고 했다. python-crontab이라는 라이브러리를 찾아내어 쓰려 했지만 가상 환경으로 쓸 경우 필요한 모듈들을 못 찾아 한참 동안 해맸다. 구글에서 많은 검색을 해보았지만 직접 crontab으로 접근한 답변 밖에 못 찾아 결국 crontab -e을 사용하여 직접 명령어를 입력하였다.

```
PYTHONPATH=/Users/damagedcase/Documents/Code States/IM14/crawler/.env/lib/python3.7/site-packages

* * * * * cd Documents && cd Code\ States && cd IM14 && cd crawler && cd mysite && cd myapi && /usr/bin/python twitch_multiprocessing.py
```

위에 명령문에서 PYTHONPATH는 모듈들이 설치되어 있는 곳을 가리킨다. python-crontab에서 이를 사용할 수 없는거 같다. 그리고 다음은 크론이 실행할 명령어인데 *들은 시간 설정을 해주는 부분이다. 현재는 매 1분마다 해당 파이썬 파일을 실행하도록 되어있다. 내일은 시간 설정을 조절하는 방법을 조사하여 특정 시간에만 돌아가도록 시도해 봐야겠다. 