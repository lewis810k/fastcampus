# COOLSMS API

1. 초기세팅
2. API 사용하기

## 초기세팅

가상환경을 생성하고 sms 프로젝트를 만든다. 

.conf, settings.py, utils를 세팅한다. 

### .conf

.conf폴더에서 settings_local.json 파일을 다음과 같이 작성한다.
```python
{
  "sms": {
    "username": "coolsms 아이디",
    "password": "비밀번호",
    "api_key": "환경설정에서 볼 수 있다.",
    "api_secret": "환경설정에서 볼 수 있다.",
    "sender_number": "보내는 사람의 번호"
  },
  "django": {
    "secret_key": "Settings.py에 있는 비밀키"
  }
}
```

-

### utils
utils는 django_app/utils에 위치하는 패키지다. 프로젝트 전반에 걸쳐 사용하기에 용이한 메소드들을 따로 구현해두고 가져다 쓸 수 있도록 한다. settings라는 패키지를 추가하고 settings와 관련된 메소드를 작성한다. 

> utils/settings/__init__.py

```python
import json
import os

from pathlib import Path

def get_config():
    return json.loads(Path(__file__).parents[3].joinpath('.conf', 'settings_local.json').open().read())
```
get_config는 settings_local.json 파일의 내용을 읽어들여서 json형태로 반환해준다. 어떤 방식으로 반환되는지 자세히 살펴보자.

실행순서는 다음과 같이 진행된다. 

`Path(__file__).parents[3]`는 현재 파일의 경로에서 상위로 3번 이동한다.  

`.joinpath('.conf', 'settings_local.json')`, 현재 경로에서 .conf/settings_local.json을 추가한다. 즉, 하위로 내려가는 경로를 생성해준다. 

`.open().read()`, 현재 파일을 열고 읽어들인다.  

`json.loads()`, 인자로 넘겨받은 객체를 json타입으로 변경시켜준다. 

-

### settings.py

비밀키를 settings_local.json으로 옮겼기 때문에 settings.py에서는 util의 함수를 config라는 변수로 할당받아 사용한다. 
```python
SECRET_KEY = config['django']['secret_key']
```

이외의 기타 세팅작업을 진행한다.

-

## API 사용하기

sms 서비스를 사용하기 위한 필수코드를 가져와서 내용을 조금만 채워넣으면 손쉽게 문자메시지를 전송할 수 있다. 

> sms/sms_test/sms.py

```python
import sys
from sdk.exceptions import CoolsmsException
from sdk.api.message import Message

from utils.settings import get_config

config = get_config()							#1

if __name__ == "__main__":

    # set api key, api secret
    api_key = config['sms']['api_key']			#2
    api_secret = config['sms']['api_secret']

    ## 4 params(to, from, type, text) are mandatory. must be filled
    params = dict()
    params['type'] = 'sms' # Message type ( sms, lms, mms, ata )
    params['to'] = '받는 사람' # Recipients Number '01000000000,01000000001'
    params['from'] = config['sms']['sender_number'] # Sender number
    params['text'] = '' # Message

    cool = Message(api_key, api_secret)
    try:
        response = cool.send(params)
        print("Success Count : %s" % response['success_count'])
        print("Error Count : %s" % response['error_count'])
        print("Group ID : %s" % response['group_id'])

        if "error_list" in response:
            print("Error List : %s" % response['error_list'])

    except CoolsmsException as e:
        print("Error Code : %s" % e.code)
        print("Error Message : %s" % e.msg)

    sys.exit()
```

`#1`  
각종 키 정보가 들어있는 파일의 내용을 이용하여 딕셔너리를 만든다.

`#2`  
api_key, api_secret을 채워넣고 params에도 받는 번호와 내용을 채워넣고 실행하면 문자메시지가 전달된다. 함께

위의 코드는 단문메시지에 대한 내용이고 API 사용법에 장문, 이미지 등을 보낼 수 있는 코드도 함께 제공한다. 

