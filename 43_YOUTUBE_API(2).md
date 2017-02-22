
### 검색화면을 POST에서 GET으로 바꾸기

>templates/video/search.html
>
```html
<h2>YOUTUBE SEARCH</h2>

<form action="">
    <div>
        겟겟 키워드:
        <input type="text" name="keyword">
    </div>
</form>

{% for video in videos %}
<div>
    <a href="https://www.youtube.com/watch?v={{ video.video_id }}">{{ video.title }}</a>
</div>

{% endfor %}
```
html에서 검색키워드를 아무것도 입력하지 않으면 다음과 같은 화면이 나타난다. 
![html-1](https://s17.postimg.org/7z275n4a7/0222_4.png)

코드상에서는 form태그의 내용에서 method를 제거하고 POST 방식이 아니기 때문에 csrf_token도 함께 제거했다. 

POST방식에서는 키워드 입력후 검색을 진행하면 내부데이터가 숨겨지기 때문에 url이 `http://localhost:8000/video/search/`로 나타나지만 GET방식에서는 `http://localhost:8000/video/search?keyword=입력한검색어`처럼 나타난다. keyword=입력한검색어 라는 부분이 추가된 형태이다. 

> video.views.py
```python
def search_view(request):
    videos = []
    keyword = request.GET.get('keyword', '').strip()			#1
    if keyword != '':											#2
        conf_path = get_json_path()
        content_json = json.loads(open(os.path.join(conf_path, 'settings_local.json')).read())
        request_result_json = (get_json_data(content_json, keyword)).json()

        for item in request_result_json['items']:
            published_date_str = item['snippet']['publishedAt']

            youtube_id = item['id']['videoId']
            title = item['snippet']['title']
            description = item['snippet']['description']
            published_date = parse(published_date_str)

            cur_item_dict = {
                'title': title,
                'description': description,
                'published_date': published_date,
                'youtube_id': youtube_id,
            }
            videos.append(cur_item_dict)

    context = {
        'videos': videos
    }

    return render(request, 'video/search.html', context)
```
`#1`  
request.GET['keyword']라고 하면 keyword라는 키값이 존재하지 않을 경우 에러를 발생시킨다. requeset.GET.get형태는 존재하지 않을 경우 none을 반환한다. 좌우공백을 제거하기 위해 strip() 내장함수를 사용한다. 

`#2`  
keyword가 빈값이 아닐 경우 if 내부의 코드를 진행한다. 페이지에 처음 접속하거나 입력값 없이 제출할 경우 if 조건에 해당되지 않는다. 

검색어를 애플이라고 입력하고 제출한 화면은 다음과 같다. (**title**과 **youtube_id**만 활용했음)

![html-2](https://s2.postimg.org/8qfnin0h5/0222_5.png)  

