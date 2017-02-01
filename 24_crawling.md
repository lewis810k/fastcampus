### Crawling 

Pycharm에서 alt+f12로 터미널 열기
터미널에서 pyenv version입력해서 가상환경이 적용된 터미널인지 확인   

`pip install beautifulsoup4`

`pip install requests`

`pip install lxml` : 속도개선을 위함

import requests했을 때 오류가 나는 것은. 파이썬 가상환경의 세팅이 파이썬 작업창까지 반영되지 않았기 때문이다. 

####linux 환경
`file` > `settings` > `Project: python` > `Project Interpreter`  
`세팅 아이콘` 눌러서 `Add Local` 클릭한 다음 경로를 아래와 같이 설정해준다.  
.pyenv 전까지는 각자의 환경에 따라 다름.  
![path](https://s28.postimg.org/htqmkpmp9/crawl.png)

![python](https://s29.postimg.org/xnegqqz2f/Screenshot_from_2017_02_01_11_40_30.png)


-

`r = requests.get('https://api.github.com/user', auth=('user', 'pass'))`   
네이버는 인증이 필요없기 때문에 auth는 하지 않는다. 

![naver](https://s27.postimg.org/684l8id43/0201_1.png)  
crawling 하고자 하는 웹사이트의 url을 그대로 복사해온다.  

```python
import requests

r = requests.get('http://comic.naver.com/webtoon/list.nhn?titleId=597447')
print(r)
```
url정보를 입력한다.   

![터미널](https://s27.postimg.org/bm3dg22ub/0201_3.png)

터미널에서 실행해서 정상작동하는지 response를 확인한다. 


```python
import requests

url = 'http://comic.naver.com/webtoon/list.nhn'	#url을 따로 저장한다.
params = {'titleId': '597447'}					#파라미터를 딕셔너리 형태로 저장한다.
r = requests.get(url, params=params)			#url과 params을 따로 보낸다. get함수에서 자동적으로 병합해준다.
print(r.url)									#병합되어 전송되는 url을 확인해본다.  
```
파라미터를 딕셔너리 형태로 보낼 수 있다.   

![터미널2](https://s28.postimg.org/h6cbubo59/0201_4.png)

r.url이 어떤 값을 가지고 있는지 확인해본다. 

-

#### Response Content 

`r.text`라고 입력하면 해당 페이지의 HTML 소스코드를 그대로 가져온다.  

```python
a_list = soup.find_all('a')
for a in a_list:
    print(a.get('href'))
```
`a` 태그를 모두 찾고 a_list 라는 변수에 리스트형태로 저장한다.  
반복문을 순회하면서 `href`를 출력한다.  

```python
td_list = soup.find_all('td', class_='title')
for td in td_list:
    print(td.get_text().strip())			#제목을 출력하는데 strip을 이용해서 제목에 포함된 좌우 공백을 제거한다. 
```
웹툰 페이지에 제목만을 출력하고 싶은 경우,   
제목은 `td` 태그에 쌓여져 있다. 모든 `td`태그를 불러오는데 class가 `title`인 `td`를 선택한다.  
(class는 파이썬에서 사용하고 있기 때문에 class_ 언더바를 넣어주어야 한다. )   

```python
tr_list = soup.find_all('tr')[2:]       # 상위에 다른 tr이 있어서 막힘
# for index, tr in enumerate(tr_list):		#몇번째 tr에서 에러가 나는지 확인
#     td_list = tr.find_all('td')
#     print('{} : {}'.format(index, len(td_list)))


for tr in tr_list:
    td_list = tr.find_all('td')
    td_thumbnail = td_list[0]
    td_title = td_list[1]
    td_rating = td_list[2]
    td_created = td_list[3]

    title = td_title.get_text(strip=True)
    created = td_created.get_text(strip=True)

    print(title, created)
```
우리가 필요한 `tr`에는 4개의 `td`가 존재한다. 슬라이싱을 따로 하지 않고 모든 경우를 처리하려면 다음과 같이 작성한다.  

```python
for tr in tr_list:
    td_list = tr.find_all('td')
    if len(td_list) < 4:
        continue
    td_thumbnail = td_list[0]
    td_title = td_list[1]
    td_rating = td_list[2]
    td_created = td_list[3]

    title = td_title.get_text(strip=True)
    created = td_created.get_text(strip=True)

    print(title, created)
```

###모듈과 패키지(추가 예정)

파일 하나가 하나의 모듈이다.   

모듈 단위로 독립된 환경, 클로저를 가진다.  


#Crawling Flow

![flow](https://s23.postimg.org/beg47ne6z/flow.png)    
예제 crawling의 흐름도  

`빨간색`은 한번만 실행되고 `보라색`은 상황에 따라 반복 횟수가 많아질 수 있다.   

![tree](https://s29.postimg.org/mvwsqiw8n/tree.png)   
프로젝트 디렉토리 정보


### #1
```
인자 : WEBTOON_ID
```

crawl.py에서 `import parser`로 parser package에 있는 정보들을 참조할 수 있도록 한다.   
```python
#crawl.py
import parser
```

`parser`는 import가 되자마자 바로 실행할 코드를 `__init__.py`에 입력한다.  
```python
#__init__.py
from .page import *
```
`__init__.py`에 page 모듈의 모든 정보(*)를 import 했기 때문에 `crawl.py`에서는 `parser.함수명` 형태로 page 모듈의 함수를 사용할 수 있게 된다. 

crawl.py에서는 parser에 있는 get_webtoon_episode_list함수를 호출한다. 

-

### #2
`get_webtoon_episode_list`에서는 list정보를 추출하기 위해서는 전체 페이지 수를 알아야 하고  
전체 페이지 수를 알기 위해서는 최신 에피소드가 몇번인지 알아야 한다.   
```python
# 특정 웹툰의 모든 에피소드 리스트를 리턴
total_number = get_webtoon_recent_episode_number(webtoon_id)    
```
`get_webtoon_recent_episode_number` 함수를 호출할 때 webtoon_id를 인자값으로 넘겨준다. 

-

### #3
최신 에피소드 값을 구하려면 처음 페이지의 HTML소스를 불러와야 한다.   
```python
soup = get_soup_from_naver_webtoon_by_page(webtoon_id)
```
불러온 소스 정보를 soup 변수에 넣는다.  
여기서 인자값으로 webtoon_id를 얻게 되는데 `get_soup_from_naver_webtoon_by_page`함수는 page 번호를 따로 넣어주지 않을 경우 자동적으로 1번 페이지를 가리키게 된다. 따라서 1번 페이지에 최신 에피소드가 있기 때문에 값을 따로 지정하지 않는다.  

-

### #4
`get_soup_from_naver_webtoon_by_page`함수는 url과 webtoon_id, page번호를 가지고 soup를 요청한다.   
필요한 웹사이트의 주소와 파라미터를 전달해주는 함수이다.  
```python
return get_soup_from_url(url, params)
```
return 할 데이터를 요청한다. 

-

### #5
`get_soup_from_url`함수는 말그대로 url로부터 soup데이터를 추출한다.  
requests 모듈을 import하여 url을 통해 요청한다. 그리고 결과값을 따로 저장한다.  
```python
r = requests.get(url, params=params)
html_doc = r.text
```
r.text는 HTML소스 전체를 반환해준다.  

```python
soup = BeautifulSoup(html_doc, 'lxml')
    return soup
```
BeautifulSoup라는 함수를 이용하여 HTML소스를 lxml타입으로 변형하여 soup를 만들고 반환한다.

-

### #6
`get_soup_from_naver_webtoon_by_page`함수는 soup(첫 페이지 정보)를 받는 즉시 
```python
return get_soup_from_url(url, params)
```
return한다 

-

### #7
첫 페이지의 HTML 소스를 이용하여 최신 에피소드 번호를 얻는다.   
```python
tr_list = soup.find_all('tr')
    recent_episode_number = None
    #만화목록 페이지의 테이블에서 매 row를 순회하며
    for tr in tr_list:
        #class가 title인 td요소를 찾는다
        td = tr.find('td', class_='title')
        if td:
            #링크주소가 있는 a요소
            a = td.find('a')
            # a요소의 href속성
            href = a.get('href')
            #정규표현식, 아무문자열이나 반복되다가
            # ?no= 또는 &no= 이후의 숫자와 매칭

            p = re.compile(r'.*[?&]no=(\d+).*')  # 패턴생성
            # [?&] 둘 중 하나를 받는다
            m = re.match(p, href)  # 기존의 href와 매칭
            # 링크주소에서 정규표현식의 패턴이 매치되었을 때
            if m:
                # 매치된 숫자를 recent_episode_number에 할당하고
                # 반복문 종료
                recent_episode_number = m.group(1)
                break

    #HTML을 파싱한 결과는 문자열이므로 정수형으로 형변환 후 리턴
    return int(recent_episode_number) #숫자로 반환

```
이 과정에서 HTML의 태그 정보를 불러와서 태그 안의 값을 찾아낸다.  
최신 에피소드 정보는 url에 `no=`형태로 저장되어 있기 때문에   
해당 번호를 추출하기 위해 `정규표현식`을 사용한다. (추후에 다룰 예정)  
링크 주소에서 정규표현식의 패턴으로 번호를 찾은 다음 int로 형변환을 해주고 반환한다.  

-

### #8
흐름도에서 보라색으로 표시된 숫자들은 반복이 발생하는 부분이다.  
빨간색 부분은 첫 페이지를 통해 최신 에피소드 값을 구하기만 했다면,  
보라색 부분에서 전체 페이지를 통해 모든 소스에 접근하는 동안 반복이 발생한다.  

```python
for i in range(total_page_number):
    cur_page_num = i + 1
    # 페이지번호와 webtoon id를 인자로 전달해서 episode dict list를 가져온다
    cur_episode_list = get_episode_list_from_page(webtoon_id, cur_page_num)
    # 현재 페이지에서 가져온 episode list를 total_episode_list리스트에 넣어준다.
    total_episode_list.extend(cur_episode_list)

return total_episode_list
```
이 코드가 보라색 전체에 대한 반복코드이다.  
페이지 번호와 webtoon id를 `get_episode_list_from_page`함수로 전달한다.  
이 함수는 넘겨받은 페이지에 존재하는 에피소드 정보를 불러오게 된다.  
그리고 최종적으로 에피소드 정보를 리스트 변수에 누적하여 반환하게 된다. 

-

### #9
`get_episode_list_from_page`함수도 페이지의 HTML 소스가 필요하기 때문에  
soup데이터를 요청해야한다. 이전과 다른 점은 최신정보만 찾는 것이 아니기 때문에   
특정 페이지 정보를 추가적으로 인자로 전달한다.  
```python
soup = get_soup_from_naver_webtoon_by_page(webtoon_id, page)
```

-

### #10 & #11
`get_soup_from_url`함수는 #4, #5와 동작이 동일하다. 한가지 다른 점은 찾아주는 페이지가 고정이냐 아니냐의 차이다.  
첫 에피소드 값을 구하기 위해서는 1페이지만 넘겨주면 되었지만  
모든 페이지의 정보를 얻기 위해서는 각각의 페이지 번호를 넘겨주어야 한다.  

-

### #12
마찬가지로 soup정보를 넘겨받는 즉시 요청했던 함수로 반환해준다.  

-

### #13
```python
def get_episode_list_from_page(webtoon_id, page=1):
    soup = get_soup_from_naver_webtoon_by_page(webtoon_id, page)
    # 리턴할 리스트
    episode_list = []
    tr_list = soup.find_all('tr')
    # for index, tr in enumerate(tr_list):
    #     td_list = tr.find_all('td')
    #     print('{} : {}'.format(index, len(td_list)))

    for tr in tr_list:
        td_list = tr.find_all('td')
        # 각 low가 td를 4개보다 적게 가지면 건너뛴다.
        if len(td_list) < 4:
            continue
        td_thumbnail = td_list[0]
        td_title = td_list[1]
        td_rating = td_list[2]
        td_created = td_list[3]

        title = td_title.get_text(strip=True)
        created = td_created.get_text(strip=True)
        #td에서 a를 찾고 href에서 주소 정보를 가져온다.
        link = td_title.find('a').get('href')

        cur_episode = {
            'title': title,
            'link': link,
            'created': created,

        }
        episode_list.append(cur_episode)
    return episode_list
```
이 함수는 `soup`정보를 이용하여 `tr`태그를 찾는다. `tr`태그에 제목, 등록일, 주소 등 추출해야 하는 정보가 담겨있기 때문이다.  
`tr`이 `td`를 4개 가지고 있어야 정상적인 데이터로 간주하기 때문에 4개 보다 적을 경우 내용을 검사하지 않는다.  
`cur_episode`변수는 현재의 에피소드 정보를 딕셔너리 형식으로 저장한다.   
최종적으로 각 에피소드의 정보들이 딕셔너리 형태로, 리스트에 저장된다.  
이렇게 모인 정보들이 가장 처음에 리스트 정보를 요청했던 `crawl.py`로 반환된다.  

-

### #14
```python
result = parser.get_webtoon_episode_list_by_no(WEBTOON_ID)

f = open('webtoon.html', 'w')
f.write('<html><body>')
for item in result:
    f.write('''
        <div>
            <a href="{href}">{title}</a>
            | <span>{created}</span>
        </div>
    '''.format(
        href=item['link'],
        title=item['title'],
        created=item['created'],
    ))
f.write('</body></html>')
f.close()
```
`result`변수는 리스트로 정리된 최종 데이터를 넘겨 받았다.  
그 아래는 파일로 만드는 과정이다.  
`result`에 담긴 정보들을 이용하여 HTML 문서를 코드로 만든다.   
자세한 내용은 파일I/O 시간에 다룰 예정이다.   

-
전체적인 흐름에 대한 설명만 하느라 자세한 코드 설명이 부족했다.  
주석이 첨부된 자세한 코드는 다음 링크를 참조하길 바란다.  
[Crawling Code](https://github.com/LeeHanYeong/Fastcampus-WPS-4th/tree/master/Projects/python/crawling)





















