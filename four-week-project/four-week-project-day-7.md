# 4주 프로젝트 7일차 (2019/09/23)

유튜브 정보를 가져오기 위해 API 키 대신에 토큰을 사용하는 방법을 알아봤다. 그래서 google oauth2 crendtial을 사용하여 토큰을 받아와 지난 주에 작성했던 코드에 적용해 보았다.

```python
import os
import json
import os.path
import google.oauth2.credentials
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build
from googleapiclient.errors import HttpError
from google_auth_oauthlib.flow import InstalledAppFlow

CLIENT_SECRETS_FILE = "client_secret.json"
SCOPES = ['https://www.googleapis.com/auth/youtube.force-ssl']
API_SERVICE_NAME = 'youtube'
API_VERSION = 'v3'
channel_id = 'UCom6YhUY62jM52nIMjf5_dw'


def get_authenticated_service():
    if os.path.isfile("credentials.json"):
        with open("credentials.json", 'r') as f:
            creds_data = json.load(f)
        creds = Credentials(creds_data['token'])
    else:
        flow = InstalledAppFlow.from_client_secrets_file(
            CLIENT_SECRETS_FILE, SCOPES)
        creds = flow.run_console()
        creds_data = {
            'token': creds.token,
            'refresh_token': creds.refresh_token,
            'token_uri': creds.token_uri,
            'client_id': creds.client_id,
            'client_secret': creds.client_secret,
            'scopes': creds.scopes
        }
        print(creds_data)
        with open("credentials.json", 'w') as outfile:
            json.dump(creds_data, outfile)
    return build(API_SERVICE_NAME, API_VERSION, credentials=creds)


def get_channel_videos(service, channel_id):
    res = service.channels().list(id=channel_id,
                                  part='contentDetails').execute()
    playlist_id = res['items'][0]['contentDetails']['relatedPlaylists']['uploads']
    videos = []
    next_page_token = None
    while 1:
        res = service.playlistItems().list(playlistId=playlist_id,
                                           part='contentDetails',
                                           maxResults=50,
                                           pageToken=next_page_token).execute()
        videos += res['items']
        next_page_token = res.get('nextPageToken')
        if next_page_token is None:
            break
    output = []
    for video in videos:
        res = service.videos().list(id=video['contentDetails']['videoId'],
                                    part='statistics').execute()
        output.append(res)
    print(output)


if __name__ == '__main__':
    os.environ['OAUTHLIB_INSECURE_TRANSPORT'] = '1'
    service = get_authenticated_service()
    get_channel_videos(service, channel_id)

```

먼저 구글 콘솔 페이지로 들어가 OAuth2 클라이언트 아이디를 만들 때 범주를 other로 선택하고 클라이언트 아이디와 시크릿이 담긴 JSON 파일을 다운 받아 파일명을 client_secret.json 바꿔주면 된다. 이 후 위의 코드를 실행하면 터미널에 URL이 주어지고 authorization code를 입력하라는 프롬트가 나타난다. Authorization code는 URL에 접속하여 구글 계정 접속을 허용하면 주어지고 터미널에 복붙해서 넣으면 credientials.json이라는 파일일 생성된다. 이 파일 안에는 토큰, 리프레쉬 토큰,  token_uri, 클라이언트 아이디, 클라이언트 시크릿, 스코프에 대한 정보가 들어있다. 이 내용들을 기반으로 두어 서비스를 인증하는 인스턴스를 만들고 주어진 채널에 대한 모든 동영상 정보를 가져오게 했다.

처음에는 리프레쉬 토큰을 사용하면 사용량에 대한 제한 없이 계속 데이터를 가져올 수 있을거라는 예상과 달리 계정마다 데이터를 쓸 수 있는 할당량이 있어 금방 막혀버렸다. 이를 우회할 수 있는 방법을 모색하다 결국 못 찾아서 일단 계정을 여러 개 만들어 API 키를 만드는 방안과 추가 할당량 지불 방안을 세웠지만 기업과 논의해봐야 확실히 알 수 있을 것 같다.