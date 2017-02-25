# FACEBOOK API(1)

1. 초기세팅  
2. FACEBOOK로그인 API  

### 초기세팅
우선 작업을 위한 가상환경을 세팅하고 facebook project와 member app을 구성한다.  

1. MyUser

커스텀유저를 세팅하기 위해 AbstractUser를 상속받는다. 

>member/models.py
```python
class MyUser(AbstractUser):
    pass
```

settings에 커스텀유저 상황을 반영한다. 
>settings.py
```
AUTH_USER_MODEL = 'member.MyUser'
```

-

2. .conf  

.conf 디렉토리는 root 디렉토리의 하위레벨에 존재한다. 각종 키를 관리하기 위한 json 파일인 settings_local.json파일을 생성한다. **`이 디렉토리는 키를 관리하기 때문에 반드시 .gitignore에 .conf를 등록하여 깃저장소에 푸쉬되지 않도록 주의한다.`** 

```python
{
  "django": {
    "secret_key": "****"
  },
  "facebook": {
    "app_id": "****",
    "secret_code": "****"
  }
}
```
>json형식은 데이터를 딕셔너리형태로 관리하며 **"쌍따옴표만"** 허용한다. 

-

3. settings.py  
각종 경로를 세팅한다. 

```python
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
TEMPLATES_DIR = os.path.join(BASE_DIR, 'templates')
ROOT_DIR = os.path.dirname(BASE_DIR)
CONF_DIR = os.path.join(ROOT_DIR, '.conf')

# load config file
config = json.loads(open(os.path.join(CONF_DIR, 'settings_local.json')).read())

# static
STATIC_URL = '/static/'
STATIC_DIR = os.path.join(BASE_DIR, 'static')
STATICFILES_DIRS = (
    STATIC_DIR,
)

# media
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```
TEMPLATES, static, media 등에 대한 경로를 설정해준다. 그리고 config는 CONF_DIR의 내용을 로드하여 json형태로 할당받는다. startproject시 설정된 비밀키는 settings_local.json파일로 옮겨졌고 이를 config를 통해 불러온다. 

```pyhon
SECRET_KEY = config['django']['secret_key']
```


### FACEBOOK로그인 API
