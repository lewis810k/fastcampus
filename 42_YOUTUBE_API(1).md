## YOUTUBE API
유튜브 API를 이용하여 특정 검색어에 대한 결과 정보를 pycharm 콘솔에 출력해보도록 한다. 

### .conf/settings_local.json

새로운 프로젝트(youtube)를 생성하고 기본 환경설정을 진행한다.  

django_app 폴더와 동일한 레벨에서 .conf라는 폴더를 생성한다.  

폴더 내부에 settings_local.json 파일을 생성한다. 

```python
{
  "youtube": {
    "API_KEY": "AIzaSyDh8WWytKv8YYpvZYFRSL3nGbPaI_aEY_4"
  }
}
```
딕셔러리를 구성하고 API_KEY 값은 [키 받는 방법](https://github.com/LeeHanYeong/Fastcampus-WPS-4th/blob/master/Django/07.%20YouTube.md)을 참고하기 바란다. 키값은 유저마다 다른 것이 정상이다. 

### youtube.py

django_app과 동일한 레벨에서 code라는 폴더를 생성한다.  

폴더 내부에 youtube.py를 생성한다. 

#### #1. 키 불러오기
YOUTUBE에 접근하기 위해서 API키 값이 필요하기 때문에 settings_local.json에 입력해두었던 키값을 불러온다. youtube.py와 settings_local.json은 서로 다른 디렉토리에 존재하기 때문에 settings_local.json의 실제 경로가 필요하다. 

`os.path.abspath(__file__)` : 현재작업중인 파일의 절대경로를 반환한다. 
`os.path.dirname(path)` : 상위 레벨로 간다.  
`os.path.join(path, 'next_path')` : 현재 경로와 next_path를 병합한다.  

현재 youtube.py에 있기 때문에   
```
youtube.py > code > youtube > .conf > settings_local.json
	1		   2	    3	    4			5
```
까지 가야한다. 

youtube 폴더까지는 상위로 올라가는 상황이고 이후에는 하위로 내려간다. 위에 명시된 경로관련함수를 이용하면 현재디렉토리를 구하고, 상위(dirname), 상위, 하위(join), 하위로 간다. 코드는 다음과 같다. 

```python
current_file_path = os.path.abspath(__file__)	#1
code_path = os.path.dirname(current_file_path)	#2
youtube_path = os.path.dirname(code_path)		#3
conf_path = os.path.join(youtube_path, '.conf')	#4
content_file_path = os.path.join(conf_path, 'settings_local.json')	#5
```

#### #2. json파일 읽기

파일을 읽을 때 open으로 열지만 해당 라인에서 .read()를 호출할 경우 메모리에 바로 올려지지 않기 때문에 close()를 따로 해주지 않아도 된다. 
```python
content = open(os.path.join(conf_path, 'settings_local.json')).read()
```
이렇게 받아온 content는 dict type이 아니라 str type이다. str type을 다시 dict으로 바꾸기 위해 json함수를 사용한다. 

```python
content_json = json.loads(content)
```
json은 따로 import 시켜준다. 이렇게 형변환하면 json에서 다루는 dict타입으로 변경된다. 

#### #3. requests.get 요청 받아오기

requests는 터미널에서 pip install requests 명령어로 설치해준다. 

```python
params = {
    'part': 'snippet',
    'q': '라디오스타',
    'key': content_json["youtube"]["API_KEY"],

}

request_result = requests.get('https://www.googleapis.com/youtube/v3/search?', params=params)
```
아래의 request.get 함수에서 키워드 인자로 params을 취하는 것을 볼 수 있다. 이 값들을 미리 만들어서 requests.get요청을 시도한다. 


