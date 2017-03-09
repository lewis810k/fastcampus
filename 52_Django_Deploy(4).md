# Django Deploy(4)

- boto3
- default-storage
- S3 주소설정

### IAM user s3권한 부여

1. AWS service에서 IAM을 클릭 
2. 권한변경을 원하는 user를 클릭
3. Add permission 버튼 클릭
4. Grant Permission 옵션 중 Attach existing policies directly 클릭
5. S3 검색 후 fullaccess 클릭 -> NEXT
6. Add Permission

### boto 3 설치

로컬의 deploy_ec2 가상환경에서 boto3를 설치한다. 
```
pip install boto3
```

python을 입력해서 shell을 띄운다. 
```python
import boto3
>>> session = boto3.Session(profile_name='default')	#1
>>> client = session.client('s3')					#2
>>> client.create_bucket(Bucket='lewis-bucket', CreateBucketConfiguration={'LocationConstraint': 'ap-northeast-2'})			#3
```
`#1` : default라는 이름으로 세션을 생성한다. default는 기본적으로 생성되어있고 ~/.aws 경로에 있는 config 파일에 저장되어있다. 이 작업은 Deploy(2)의 aws configure 작업에서 진행했다.   
`#2` : s3 service를 사용할 클라이언트를 설정한다.  
`#3` : 중복되지 않는 이름으로 Bucket을 생성한다. 지역 설정을 'ap-northeast-2'로 한다. (서울지역)

생성이 정상적으로 되었다면 AWS 사이트의 S3에서 생성된 bucket을 확인할 수 있다. 
![0309-1](https://s10.postimg.org/hl25bdmqx/0309_1.png)

-

### default-storage 설정

설치

```
pip install django-storages

pip freeze > requirements.txt

INSTALLED_APPS에 'storages'추가
```

>settings.py
```
STORAGE_S3 = os.environ.get('STORAGE') == 'S3' or DEBUG is False

.
.
.

if STORAGE_S3:
    DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
else:
    STATIC_ROOT = os.path.join(ROOT_DIR, 'static_root')
    STATIC_URL = '/static/'
```
서버를 실행할 때는 S3를, 로컬로 실행할 때는 로컬의 static을 사용하도록 한다.

settings_common.json에서 aws 정보 입력. key정보는 ~/.aws 경로에 있는 credentials에서 확인할 수 있다.

```
"aws": {
    "access_key_id": "",
    "secret_access_key": "",
    "s3_storage_bucket_name": "lewis-bucket"
    "s3_region": "ap-northeast-2"
  }
```
> **git 작업시 반드시 ignore되도록 해야한다.**

>settings.py
```
AWS_ACCESS_KEY_ID = config['aws']['access_key_id']
AWS_SECRET_ACCESS_KEY = config['aws']['secret_access_key']
AWS_STORAGE_BUCKET_NAME = config['aws']['s3_storage_bucket_name']
AWS_S3_HOST = 's3.{}.amazonaws.com'.format(config['aws']['s3_region'])
```

config가 common의 내용을 합치는 코드 다음에 config에 접근해야함.

*** collectstatic

```
MODE='DEBUG' STORAGE='S3' ./manage.py collectstatic
```
정상작동할 경우 AWS S3에서 bucket을 누를 경우 올라간 파일들이 확인된다. 

-

### signature error 발생할 경우

settings_common.json의 aws 부분에 다음을 추가한다. 
```
"s3_signature_version": "s3v4",
```

setting.py에도 AWS 내용을 추가한다. 
```
AWS_S3_SIGNATURE_VERSION = config['aws']['s3_signature_version']
AWS_S3_CUSTOM_DOMAIN = '{}.s3.amazonaws'.format(AWS_STORAGE_BUCKET_NAME)
```

-

실제 S3가 저장된 url을 가지고 와야 한다. 

### S3 주소 설정

S3에 업로드된 이미지에서 링크정보를 살펴보면 다음과 같다. 
```
https://s3.ap-northeast-2.amazonaws.com/lewis-bucket/images/moon.jpg
```
이 정보를 이용하여 settings.py의 STATIC_URL을 변경한다.   
(S3를 사용하는 모드로 서버를 열었을 때)
```
if STORAGE_S3:
    DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    STATIC_URL = 's3.{region}.amazonaws.com/{bucket_name}/'.format(
        region=config['aws']['s3_region'],
        bucket_name=config['aws']['s3_storage_bucket_name']
    )
```

> MODE='DEBUG' STORAGE='S3' ./manage.py runserver 혹은 manage_s3 runserver

![0309-8](https://s24.postimg.org/txfj3j0w5/0309_8.png)

localhost로 들어가서 이미지의 경로를 살펴보면 S3에서 받아오는 것을 확인할 수 있다. 








```








```

