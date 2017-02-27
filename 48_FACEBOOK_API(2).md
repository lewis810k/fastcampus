# FACEBOOK 로그인

전체 흐름은 [FACEBOOK 로그인](https://developers.facebook.com/docs/facebook-login/manually-build-a-login-flow)을 참조한다. 

## 사용자 로그인 유도

### 로그인 대화 상자 호출 및 리디렉션 URL 설정

앱에서 로그인 대화상자를 표시할 엔드포인트로 리디렉션 해야한다. 

> member/views.py

```python
def login_fbv(request):
    facebook_app_id = settings.config['facebook']['app_id']
    context = {
        'facebook_app_id': facebook_app_id,
    }
    return render(request, 'member/login.html', context)
```
facebook_app_id는 settings_local.json 파일에서 불러온다.  
member/login.html와 렌더링을 할 때 facebook_app_id를 context에 실어서 넘겨준다. 

```html
{% extends 'common/base.html' %}

{% block content %}
<div class="login-container">
    <h1>FACEBOOK LOGIN</h1>
    <a href="https://www.facebook.com/v2.8/dialog/oauth?client_id={{ facebook_app_id }}&redirect_uri=http://localhost:8000/member/login/facebook/">페이스북으로 로그인</a>
</div>
{% endblock %}
```
화면은 다음과 같이 a tag만 하나 존재한다. 

![fb-1](https://s18.postimg.org/n3r59izkp/0227_1.png)

a tag를 누르면 어떤 일이 발생하는지 살펴보자.  

```
https://www.facebook.com/v2.8/dialog/oauth?		#1
client_id={{ facebook_app_id }}					#2
&redirect_uri=http://localhost:8000/member/login/facebook/  #3
```
`#1` : facebook의 oauth로 로그인을 위한 요청을 보낸다.   
`#2` : 앱의 대시보드에 있는 앱 ID 
`#3` : 요청작업 완료 후 이동할 uri정보. urls.py에 의해 해당 뷰로 이동한다.  

```python
url(r'^login/facebook/$', views.login_facebook, name='login_facebook'),
```
/member/login/facebook은 url에 의해 views.login_facebook로 연동된다.   

## ID 확인

앱에서 액세스 토큰을 생성하기 전에 앱 사용자가 응답 데이터가 있는 사용자인지 확인해야 한다. `code가 수신되면` 엔드포인트를 사용하여 액세스 토큰과 교환해야 한다. 이 호출에서는 앱 시크릿 코드가 사용되므로 서버 간에 이루어져야 한다. 

> member/views.py

```python
def login_facebook(request):
	# ----------------- 1 ------------------------
    APP_ID = settings.config['facebook']['app_id']
    SECRET_CODE = settings.config['facebook']['secret_code']
    REDIRECT_URI = 'http://localhost:8000/member/login/facebook/'
    APP_ACCESS_TOKEN = '{app_id}|{secret_code}'.format(
        app_id=APP_ID,
        secret_code=SECRET_CODE,
    )
  	# ----------------- 1 ------------------------

  	# ----------------- 2 ------------------------
    if request.GET.get('code'):
        code = request.GET.get('code')
        url_request_access_token = 'https://graph.facebook.com/v2.8/oauth/access_token'
        params = {
            'client_id': APP_ID,
            'redirect_uri': REDIRECT_URI,
            'client_secret': SECRET_CODE,
            'code': code,  # <---- 코드값
        }

        r = requests.get(url_request_access_token, params=params)
        dict_access_token = r.json()
        USER_ACCESS_TOKEN = dict_access_token['access_token']
   		# ----------------- 2 ------------------------

      	# ----------------- 3 ------------------------
        url_debug_token = 'https://graph.facebook.com/debug_token'
        params = {
            'input_token': USER_ACCESS_TOKEN,
            'access_token': APP_ACCESS_TOKEN,
        }
        r = requests.get(url_debug_token, params=params)
        dict_debug_token = r.json()
        USER_ID = dict_debug_token['data']['user_id']
      	# ----------------- 3 ------------------------

		# ----------------- 4 ------------------------
        url_api_user = 'https://graph.facebook.com/{user_id}'.format(
            user_id=USER_ID,
        )
        fields = [
            'id',
            'first_name',
            'last_name',
            'gender',
            'picture',
            'email',
        ]
        params = {
            'fields': ','.join(fields),
            'access_token': USER_ACCESS_TOKEN,
        }
        r = requests.get(url_api_user, params)
        dict_user_info = r.json()
        # ----------------- 4 ------------------------

		# ----------------- 5 ------------------------
        user = authenticate(facebook_id=USER_ID, extra_fields=dict_user_info)
        login(request, user)
        return redirect('index')
        # ----------------- 5 ------------------------
```
login_fbv에서 페이스북 로그인으로 이동 후, redirect_uri를 이용해 다시 login_facebook으로 돌아온 후의 동작

#### #1
config에서 app_id와 secret_code를 불러온다. 이 정보들을 이용하여 이후에 토큰검증을 위한 access token을 생성한다. 

#### #2. 액세스 토큰의 코드 교환
login_fbv에서 정상적으로 로그인이 되었다면 request.GET에 "code"정보가 같이 실려온다. 이 code값을 이용하여 access token을 얻는다. OAuth 엔드포인트에 HTTP GET 요청을 하려면 필수 매개변수값을 지정해주어야 한다.

```python
GET https://graph.facebook.com/v2.8/oauth/access_token?
   client_id={app-id}
   &redirect_uri={redirect-uri}
   &client_secret={app-secret}
   &code={code-parameter}
```
- client_id : 앱 ID
- redirect_uri : 필수 인수이며 로그인 프로세스를 시작할 때 사용한 request_uri와 같아야 한다. /member/login/facebook으로 현재 뷰로 request 했기 때문에 이 값과 같이 사용한다. 
- client_secret : 앱 대시보드에 있는 고유 앱 시크이 엔드포인트는 다음과 같은 매개변수를 사용합니다.

릿 코드
- code : 로그인 대화상자가 리디렉션할 때 수신한 매개변수

필수 매개변수들을 채워서 request를 하면 다음과 같이 JSON형식으로 반환된다. 
```python
{
  "access_token": {access-token}, 
  "token_type": {type},
  "expires_in":  {seconds-til-expiration}
}
```
JSON형식을 갖췄다는 것이지 실제로는 str타입으로 넘겨받는다. 따라서 .json()함수로 형변환을 해준다. 우리가 필요한 ['access_token']을 추출한다. 


#### #3. 액세스 토큰 검사
API 엔드포인트를 이용하여 토큰의 검사를 자동화한다. 엔드포인트는 다음과 같다. 

```html
GET graph.facebook.com/debug_token?
     input_token={token-to-inspect}
     &access_token={app-token-or-admin-token}
```
- input_token : 검사가 필요한 토큰
- access_token : 앱 개발자의 액세스 토큰 또는 앱 액세스 토큰

정상적으로 검사가 되었다면 다음과 같은 형식의 데이터를 반환한다. 

```python
{
    "data": {
        "app_id": 138483919580948, 
        "application": "Social Cafe", 
        "expires_at": 1352419328, 
        "is_valid": true, 
        "issued_at": 1347235328, 
        "metadata": {
            "sso": "iphone-safari"
        }, 
        "scopes": [
            "email", 
            "publish_actions"
        ], 
        "user_id": 1207059
    }
}
```
app_id와 user_id 필드를 통해 앱에서 사용자와 앱에 유효한 액세스 토큰을 확인할 수 있다. 우리가 필요한 것은 user_id이므로 따로 변수화한다.

#### #4. 유저정보 받아오기
그래프 API를 이용하여 USER 개인의 정보를 받아올 수 있다. 






```

















```




