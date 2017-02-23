### 북마크 등록/해제 
1. 북마크 버튼 등록, 해제 만들기 

view에서 현재 보여주는 video가 북마크 된 상태인지 아닌지를 is_exist 값으로 넘겨준다. video.is_exist가 true이면 '북마크해제' 버튼이 생성된다. 


> templates/video/search.html

```html
<form action="{% url 'video:bookmark_add' video_id=video.video_id %}" method="POST" style="display:inline">
        {% csrf_token %}
        <input type="hidden" name="url" value="{{ request.get_full_path }}">
        {% if video.is_exist %}
        <button type="submit" class="btn">북마크해제</button>
        {% else %}
        <button type="submit" class="btn">북마크등록</button>
        {% endif %}
    </form>
```
'북마크해제'나 '북마크등록'버튼을 누르면 url을 통해 bookmark_add 뷰로 넘어가고 이 때 해당 video의 id값을 파라미터로 넘겨준다. 

url은 다음과 같다. `<video_id>`는 키값을 받아오기 위한 정규표현식을 사용한다.   

```python
url(r'^add/bookmark/(?P<video_id>[a-zA-Z0-9-_]+)/$', views.bookmark_add, name='bookmark_add'),
```

뷰의 bookmark_add는 url로부터 video_id를 받아 북마크등록/해제 작업을 완료하고 현재 페이지로 다시 돌려보낸다. 
```python
@login_required
def bookmark_add(request, video_id):
    if request.method == 'POST':
        
        #1
        request_result_json_items = get_json_data_for_video(video_id)
        
        #2
        snippet = request_result_json_items[0]['snippet']

		title = snippet['title']
        published_date = parse(snippet['publishedAt'])
        description = snippet['description']
        url_thumbnail = snippet['thumbnails']['high']['url']

		#3
		defaults = {
            'title': title,
            'description': description,
            'published_date': published_date,
            'url_thumbnail': url_thumbnail,
        }
		
        video, _ = MyVideo.objects.get_or_create(
            defaults=defaults,

            video_id=video_id,
            user=request.user,
        )
		
        #4
        user = MyUser.objects.get(username=request.user)
        user.toggle_bookmark(video)
		
		#5        
        url = request.POST.get('url', '').strip()
        return redirect(url)
```
`#1`  
넘겨받은 video_id를 이용하여 새로운 특정 id에 대한 정보를 반환해주는 youtube API request를 진행한다. 그리고 request의 정보를 json형태로 반환한다. 

>https://www.googleapis.com/youtube/v3/videos 에서 params을 잘 만들면 된다. 자세한 사항은 API문서 참조. 

`#2`  
json형태의 반환값으로부터 필요한 정보를 추출해낸다. 

`#3`  
새로운 MyVideo 객체를 생성하거나 이미 존재하는 객체를 받아 video로 정의한다. 모델에서는 video_id와 user를 합친 형태가 unique해야한다. 

`#4`  
현재 request.user의 정보를 가지는 MyUser 객체를 추출한다. 해당 유저에서 toggle_bookmark 메소드를 실행하면 넘겨주는 video객체에 대해 북마크등록 또는 해제 작업을 진행하고 DB에 반영한다. 

`#5`  
request를 보낸 url의 정보를 받아 redirect한다. 즉, 현재페이지로 redirect한다. 

2. 기능 구현

MyUser모델에 있는 북마크 작업 메소드이다. 

> member/models/MyUser

```python
    def toggle_bookmark(self, video):
        if self in video.bookmark_user_set.all():
            self.bookmarkvideo_set.get(video=video).delete()
            # 더이상 북마크가 없는 비디오는 DB에서삭제.
            if video.bookmarkvideo_set.count() == 0:
                video.delete()
        else:
            self.bookmarkvideo_set.create(
                video=video
            )
```
이 메소드는 북마크 작업에 재료가 되는 video를 인자값으로 넘겨받는다. if조건문을 살펴보면, video를 북마크로 지정한 유저의 리스트를 살펴본다. 그 중 현재 로그인 되어있는 유저가 존재하면 True가 된다. 

True일 경우, 현재 유저의 북마크 정보에서 넘겨받은 video를 제거한다. 그리고 제거된 video를 북마크하고 있는 유저가 한명도 없을 경우 해당 video를 DB에서 삭제한다. 

False일 경우, 현재 유저의 북마크 리스트에 video를 추가한다. 
