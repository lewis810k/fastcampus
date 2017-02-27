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
GET /v2.8/{user-id} HTTP/1.1
Host: graph.facebook.com
```
`graph.facebook.com/{user-id}`형식으로 요청을 한다. 이 요청을 할 때 기본적으로 모든 정보가 반환되지는 않는다. fields 검색 매개변수를 사용하여 반환될 필드를 선택할 수 있다. 

예를 들어, 다음 문장은 특정 유저의 id, name, picture 필드만 추출한다. 
```
https://graph.facebook.com/user_id?fields=id,name,picture
```
views.py에서도 원하는 필드들을 따로 지정하여 요청할 때 사용한다. 그리고 이전과 마찬가지로 요청에 대한 응답을 json형식으로 변환한다. 

[그래프 API 사용](https://developers.facebook.com/docs/graph-api/using-graph-api/)

#### #5. 인증 및 로그인
응답받은 정보들을 이용하여 authenticate 메소드를 실행하고 이에 대한 반환값으로 유저 객체를 받는다. 정상적으로 인증이 완료될 경우 해당 유저객체를 이용하여 로그인을 진행하고 메인페이지로 redirect한다. 

-


### AUTHENTICATE

authenticate 메소드를 사용하면 백엔드에 기본적으로 내장되어 있는 인증 메소드를 호출한다. 여기에 추가적으로 커스텀메소드를 적용하려면 settings.py에 다음과 같이 입력한다.

>settings.py

```python
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'member.backends.FacebookBackend',
]
```
입력된 순서대로 모든 authenticate를 진행한다. 하나라도 인증되지 않으면 거부된다.  


>member/backends.py

```python
class FacebookBackend():
    def authenticate(self, facebook_id, extra_fields=None):

		# --------  유저 객체 관련  ----------
        defaults = {
            'first_name': extra_fields.get('first_name', ''),
            'last_name': extra_fields.get('last_name', ''),
            'email': extra_fields.get('email', ''),
            'gender': extra_fields.get('gender', ''),
        }
        
        user, user_created = MyUser.objects.get_or_create(
            username=facebook_id,
            defaults=defaults,
        )
        
        if user_created == 1:
            # --------  프로필 이미지 추출  -----------
            url_profile = 'https://graph.facebook.com/{user_id}/picture'.format(
                user_id=facebook_id,
            )
            params = {
                'type': 'large',
                'width': '500',
                'height': '500',
            }

            temp_file = NamedTemporaryFile(delete=False)
            r = requests.get(url_profile, params, stream=True)
            _, file_ext = os.path.splitext(r.url)

            file_ext = re.sub(r'(\.[^?]+).*', r'\1', file_ext)

            file_name = '{}{}'.format(
                facebook_id,
                file_ext,
            )

            for chunk in r.iter_content(1024):
                temp_file.write(chunk)

            # --------  프로필 이미지 추출 끝 -----------
            user.img_profile.save(file_name, File(temp_file))
        return user

    def get_user(self, user_id):
        try:
            return MyUser.objects.get(id=user_id)
        except MyUser.DoesNotExist:
            return None
```
인증 작업은 최종적으로 유저객체를 반환한다. 크게 보면 프로필 이미지를 다운받는 것과 유저 객체를 생성하는 작업으로 구분된다. 프로필 이미지 추출은 밑에서 자세히 다루도록 하겠다. 

authenticate 메소드는 extra_fields라는 이름의 매개변수를 취한다. 이 값들은 defaults에 할당된다. 한 줄만 살펴보자. 
```
'first_name': extra_fields.get('first_name', ''),
``` 
이 코드는 extra_fields에 first_name이라는 키가 존재한다면 그에 대한 값을 'first_name'에 할당하라는 명령이다.  

그 다음 defaults를 부가적인 정보로, 그리고 username으로 facebook_id를 가지는 유저객체를 생성한다. 만약 이미 존재한다면 get을 한다. facebook_id는 view level에서 USER_ID로 사용했던 값이다. 

만약 새로 생성되었다면 이미지를 추출하여 유저객체에 저장한다. 그리고 유저객체를 인증요청했던 곳으로 반환한다. 

-

### 프로필 이미지 추출

```python
url_profile = 'https://graph.facebook.com/{user_id}/picture'.format(
                user_id=facebook_id,
            )
            params = {
                'type': 'large',
                'width': '500',
                'height': '500',
            }

            r = requests.get(url_profile, params, stream=True)
			print(r.url)	#1
			_, file_ext = os.path.splitext(r.url)
			print(file_ext)	#2
            file_ext = re.sub(r'(\.[^?]+).*', r'\1', file_ext)
			print(file_ext)	#3
            file_name = '{}{}'.format(
                facebook_id,
                file_ext,
            )
            print(file_name)	#4

            temp_file = NamedTemporaryFile(delete=False)
            for chunk in r.iter_content(1024):
                temp_file.write(chunk)
```

[User Picture](https://developers.facebook.com/docs/graph-api/reference/user/picture/)에서 관련 정보를 확인할 수 있다. params으로 height, width, redirect, type을 줄 수 있으며 이 중에서 type은 `enum{small, normal, album, large, square}` 다양한 옵션 중에서 선택할 수 있다.

requests를 요청할 때 `stream=True`옵션을 주면 iter_content로 미디어 파일을 다운로드할 수 있다. 우선 파일을 다운로드 하기 전에 url로부터 파일명을 추출해낸다.

하나씩 출력해보자. 
`#1`에서 r.url을 출력해보면 정리되지 않은 듯한 느낌을 주는 문자열이 출력된다.
```
https://scontent.xx.fbcdn.net/v/t1.0-1/p720x720/10956522_10153288710608838_8140479903644926392_n.jpg?oh=ab250b013e535a8e4a4bbdd35fcc7718&oe=5930C2FD
```
os.path.splitext(r.url)은 파일명과 확장자로 split을 한다. 여기서 확장자는 `.jpg`이다. 확장자를 기준으로 나누고 file_ext는 뒷부분에 해당되기 때문에 `#2`에서 출력해보면 다음과 같은 문자열을 가진다.
```
.jpg?oh=ab250b013e535a8e4a4bbdd35fcc7718&oe=5930C2FD
```
`#3`을 출력하기 전에 정규표현식을 이용하여 문자열을 replace한다. 이 때 확장자만 빼고 나머지는 제거된다. 따라서 출력해보면 다음과 같이 확장자만 출력된다. 
```
.jpg
```
각 유저의 ID정보는 고유하기 때문에 프로필 사진 이름을 `유저ID.jpg`로 저장해도 문제가 되지 않는다. 이 작업이 `#4`직전에 이루어진다. 따라서, `#4`를 출력해보면 의도한 값이 출력된다. 
```
10155034820888838.jpg
```
파일이름은 이런 방식으로 결정되지만 실제 데이터는 어떻게 얻어질까. 

temp_file은 임시파일형태를 취한다. 아무값도 가지지 않은 임시파일에 이미지의 내용을 직접 읽어서 저장한다. 위에서 stream=True옵션을 주었기 때문에 요청에 대한 응답인 r에 대해 iter_content함수를 사용할 수 있다. 응답에서 같이 실려온 데이터를 iter_content가 순회한다. 1024바이트씩 끊어서 전체를 읽을 때까지 temp_file에 계속 쓴다. 다운로드가 되는 상황이다. 

다운로드가 완료되면 user.img_profile에 저장된다. 
