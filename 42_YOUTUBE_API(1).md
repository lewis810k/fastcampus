## YOUTUBE API
유튜브 API를 이용하여 특정 검색어에 대한 결과 정보를 pycharm 콘솔에 출력해보도록 한다. 

### .conf/settings_local.json

새로운 프로젝트(youtube)를 생성하고 기본 환경설정을 진행한다.  

django_app 폴더와 동일한 레벨에서 .conf라는 폴더를 생성한다.  

폴더 내부에 settings_local.json 파일을 생성한다. 

```python
{
  "youtube": {
    "API_KEY": "------------AIzY_4"
  }
}
```
딕셔러리를 구성하고 API_KEY 값은 [키 받는 방법](https://github.com/LeeHanYeong/Fastcampus-WPS-4th/blob/master/Django/07.%20YouTube.md)을 참고하기 바란다. 키값은 유저마다 다른 것이 정상이다. 

### youtube.py

django_app과 동일한 레벨에서 code라는 폴더를 생성한다.  

폴더 내부에 youtube.py를 생성한다. 

-

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

-

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

-

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

params가 취할 수 있는 키값은 다양하다. [더 알아보기](https://developers.google.com/youtube/v3/docs/search/list)

다큐먼트에 따르면 `'part': 'snippet'`은 다음과 같이 설명되어있다. 
>snippet 속성은 결과의 제목, 설명 등을 식별하는 다른 속성을 포함합니다. part=snippet을 설정하는 경우 API 응답은 중첩된 속성도 모두 포함합니다.

q는 검색어를 말한다. 그리고 key는 API를 사용하기 위한 일종의 검증을 해준다. `#2`에서 content_json은 파일내의 모든 내용은 딕셔너리 형태로 가지고 있기 때문에 `content_json["youtube"]["API_KEY"]`로 키값에 접근할 수 있다. 중첩된 딕셔너리 구조가 잘 이해가 되지 않는다면 딕셔너리만 따로 공부해보는 것이 좋을 것 같다. 

>API를 다룰 때는 특히 딕셔너리와 리스트를 많이 사용하기 때문에 이 둘의 특성을 잘 이해하는 것이 시간을 많이 절약해줄지도 모른다. 

검색결과수를 따로 지정하지 않으면 디폴트값으로 5개를 반환한다. 

request_result.url을 출력해보면 다음과 같다.  
```
https://www.googleapis.com/youtube/v3/search?q=%EB%9D%BC%EB%94%94%EC%98%A4%EC%8A%A4%ED%83%80&key=AIzaSyDh8WWytKv8YYpvZYFRSL3nGbPaI_aEY_4&part=snippet
```
길지만 자세히 보면 아주 간단하다. 우리가 params로 넘겨준 값이 물음표 기호 뒤에서 `key=value&key=value&key=value`형식으로 입력되었다. 

-

#### #4. 전송받은 결과값을 다시 json형식으로 변환
파이썬에서 사용하기 위해 다시 json형식으로 바꾸는 과정을 매우 간단하다. .json()만 붙여주면 해당 데이터를 json화 시켜준다. 

```python
request_result_json = request_result.json()
```

> 이 객체를 그대로 print 해보면 정렬되지 않은 딕셔너리 형태로 출력된다. 매우 보기가 불편하다. pprint를 import하고 pprint(request_result_json)으로 출력하면 들여쓰기가 모두 적용된 상태로 출력된다.

pprint(request_result_json)의 일부이다. 
![image_json](https://s3.postimg.org/mryek8ihf/0222_2.png)

-

#### #5. 검색결과를 반복문을 이용하여 적절히 출력하기

위의 사진에서 본 것처럼 꽤 많은 데이터를 넘겨받았다. 데이터가 많아지면 복잡해보이기 때문에 우선 title에 대해서만 먼저 해본다.

```python
items = request_result_json['items']

for item in items:
	print('title: ', item['snippet']['title'])
```
다른 부분은 다 딕셔너리로 처리되어있지만 'items'키는 value로 리스트형식을 취한다. request_result_json이 최상위 루트를 가리키고 있으므로 'items'만 따로 접근한다. items변수 안에는 다음과 같은 형식으로 데이터가 저장되어 있다. 
```python
items = [dict1, dict2, dict3, ditc4, ...]
```

그 다음 우리가 원하는 title은 items['snippet']안에 들어있다. items의 각 dict를 돌면서 모든 title을 추출할 수 있다. 

```
#출력결과

title:  신정환 레전드 라디오스타
title:  [라디오스타]서현철님 아내 이야기!!  김국진 윤종신 웃느라 진행 못함 [황존 TV]#서현철 #연극배우
title:  황금어장 라디오스타 514회 – 독을 품은 남자들 특집 - Gyu-hyun surrounding Kim Gura and shindong a war of nerves!
title:  라디오스타 2009
title:  라디오스타 2008
```

-

#### #추가. 검색결과 계속 받아오기
매번 API정보를 불러올 때 마다 다음 페이지가 존재하면 API반환시에 nextPageToken값을 같이 받는다. 이 정보를 이용하여 다음 요청을 진행하면 검색결과를 추가적으로 받아올 수 있다. 

![nextpage](https://s22.postimg.org/ti26j2wtd/0222_3.png)

```python
while request_result_json['nextPageToken'] != None:
    for item in request_result_json['items']:
        print('title: ', item['snippet']['title'])
    params = {
        'part': 'snippet',
        'q': '라디오스타',
        'key': content_json["youtube"]["API_KEY"],
        'pageToken': request_result_json['nextPageToken']
    }
    request_result = requests.get('https://www.googleapis.com/youtube/v3/search?', params=params)
    request_result_json = request_result.json()
```
nextPageToken이 None이라는 것은 현재검색결과가 마지막 페이지라는 것이다. 마지막페이지가 아니라면 계속해서 다음 페이지에 대한 요청을 진행하도록 하는 코드이다. 


