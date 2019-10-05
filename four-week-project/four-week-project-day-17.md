# 4주 프로젝트 17일차 (2019/10/05)

오늘도 거의 진전 없는 하루였다.  수정한 유저 아이디와 유저 네임 확인하는 작업부터 먼저했는데 트위치 API를 보니 유저 네임을 가지고 유저 아이디를 받아올 수 있다는 것을 알게됐다. 

```python
import requests
from multiprocessing import Pool

headers = {'Client-ID': '< your client id > ',
           'referer': 'https://twitch.tv'}

user_names = [...]

def get_user_id():
    user_ids = []
    for name in user_names:
        r = requests.get('https://api.twitch.tv/helix/users?login=' + name, headers=headers)
        user = r.json()
        
        if user == {'data': []} or user is None:
            user_ids.append('none')
        else:
            id = user['data'][0]['id']
            user_ids.append(id)
    print(user_ids)

get_user_id()
```

만일 위에 코드와 동일하게 미리 했었으면 적어도 하루는 아낄 수 있었을텐데 아쉬웠다.

그 다음에 멀티프로세스로 db에 데이터를 넣는 작업을 진행하려고 했는데 foreign key 문제로 인해 계속 진전이 없었다. import 문제로 인해 model과 serializer가 import가 안 되어 내일 다시 해봐야 될 거 같다.