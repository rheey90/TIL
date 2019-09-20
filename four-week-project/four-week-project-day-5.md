# 4주 프로젝트 5일차 (2019/09/20)

오늘은 트위치 API가 하루에 몇 개 요청까지 허용하는지 알아보기 위해서 며칠 전에 만들었던 객체 메소드들을 반복문으로 1000번을 돌려 약 2000개의 요청을 보냈는데 다행히 막히는 일은 없었다. 이 외에 EC2 서버 세팅과 유투브 API 관련 작업을 주로 했다. EC2 서버 세팅 같은 경우는 파이썬 버전 관리와 mysql 설치에서 계속 문제가 발생하였다. 아마 내일 해결해야 할거 같다.

유투브 API 같은 경우는 도큐멘트를 이해하는데 시간이 걸렸다. 다행히 유투브 API 사용법에 대한 동영상에서 한 채널의 모든 동영상을 불러오는 가져오는 작업을 보여주어 이를 토대로 각 동영상의 조회 수, 좋아요 수, 싫어요 수, 즐겨찾기 수, 댓글 수에 대한 정보를 얻어올 수 있었다. 파이썬에서 HTTP 요청을 처리하는 라이브러리들이 다양한거 같다. 유투브 API 처리할 때는 구글에서 만든 API를 쉽게 활용할 수 있도록 만든 google-api-python-client를 사용하면 편리하다고 생각된다.

```python
from apiclient.discovery import build
api_key = '< your api key>'
youtube = build('youtube', 'v3', developerKey=api_key)
channel_id = '< channel id >'
def get_channel_videos(channel_id):
    # get Uploads playlist id
    res = youtube.channels().list(id=channel_id,
                                  part='contentDetails').execute()
    playlist_id = res['items'][0]['contentDetails']['relatedPlaylists']['uploads']
    videos = []
    next_page_token = None
    while 1:
        res = youtube.playlistItems().list(playlistId=playlist_id,
                                           part='contentDetails',
                                           maxResults=50,
                                           pageToken=next_page_token).execute()
        videos += res['items']
        next_page_token = res.get('nextPageToken')
        if next_page_token is None:
            break
    output = []
    for video in videos:
        res = youtube.videos().list(id=video['contentDetails']['videoId'],
                                    part='statistics').execute()
        output.append(res)
    return output
all_videos = get_channel_videos(channel_id)
print(all_videos)
```

다음과 같은 코드를 사용하여 필요한 정보들을 구했다. 유투브 API 도큐멘트를 보면 각 요청마다 작업과 반환할 데이터 파트들이 나와있는데 이를 활용하여 본인이 필요한 정보를 얻을 수 있다.