### pgAdmin 설정하기

home/pgadmin 디렉토리를 생성한다.  
pgadmin에 대한 pyenv 가상환경을 만든다.   
```
wget https://ftp.postgresql.org/pub/pgadmin3/pgadmin4/v1.2/pip/pgadmin4-1.2-py3-none-any.whl
```
여기서 에러 발생하면 libpq-dev 설치 
```
sudo apt-get install libpq-dev
```
```
pip install pgadmin4-1.2-py3-none-any.whl
python ~/.pyenv/versions/3.5.2/envs/pgadmin_env/lib/python3.5/site-packages/pgadmin4/pgAdmin4.py
```

![0308-2](https://s3.postimg.org/m1lr8y27n/0308_2.png)

<정상실행 메시지>

`localhost:5050`에 접속해서 확인해본다. 

![0308-3](https://s14.postimg.org/yqspkx6gh/0308_3.png)

<정상작동화면>

## postgresql 설정(로컬)
![0308-5](https://s14.postimg.org/88a2c77qp/0308_5.png)
-s는 superuser, -P는 password를 의미한다. 관리자 권한으로 패스워드를 가진 슈퍼유저를 생성하는 명령어.

#### 서버생성
![0308-6](https://s14.postimg.org/eahp2ow6p/0308_6.png)

pgAdmin 페이지에서 server general에 localhost를 입력한다.

![0308-7](https://s14.postimg.org/6j0z44s1d/0308_7.png)

connection에도 이름을 localhost로 지정하고 방금 생성한 유저정보를 입력한다.

![0308-8](https://s17.postimg.org/rdd3c3vwv/0308_8.png)

데이터베이스를 생성한다. 

![0308-9](https://s17.postimg.org/9olckhk5r/0308_9.png)

생성된 데이터베이스는 pgAdmin에서 확인할 수 있다.

![0308-10](https://s17.postimg.org/ebrem97in/0308_10.png)

settings_local.json의 내용을 업데이트한다. 비밀번호는 각자가 설정한 유저비밀번호!

![0308-11](https://s17.postimg.org/uo1gbzlu7/0308_11.png)

settings.py의 DATABASES 부분을 업데이트한다.

psycopg2설치 - 이거 왜 했는지 까먹음.. 
```
pip install psycopg2
migrate
```

![0308-4](https://s14.postimg.org/vxzi0w641/0308_4.png)

.conf-secret/settings_deploy.json 파일을 생성한다. 이 때 db 부분은 local.json과 동일하게 작성한다.

![0308-12](https://s17.postimg.org/tn17mv4un/0308_12.png)

settings.py의 상단에서 DEBUG변수를 모드에 따라 다른 값이 부여되도록 설정한다. 

![0308-14](https://s10.postimg.org/ogcgm6l21/0308_14.png)

디버그모드이면 local.json으로, 아니면 deploy.json으로 config를 설정한다.

앞으로 로컬에서 작업할 때는 다음과 같이 MODE변수값을 넣어주면서 실행한다.  
MODE='DEBUG' ./manage.py runserver
>이 문장을 간단한 명령어로 만들 수 있다. 아래 [runserver 커스텀 명령어 만들기] 참조.

-

## postgresql 설정(서버)
변경사항을 scp 명령어를 이용하여 서버로 전송한다. (전송하는 부분은 50번 파일 참고)

postgresql을 설치한다.
```
sudo apt-get update 
sudo apt-get install postgresql postgresql-contrib
```

#### 유저생성
```
sudo -u postgres createuser -s -P lewis
sudo -u postgres createdb deploy_ec2 –owner=lewis
```
migrate 하면 postgresql이 반영되고 정상작동 해야한다

-

### runserver 커스텀 명령어 만들기

home경로에서 .scripts 폴더를 생성한다.
.scripts폴더에서 manage파일을 생성하고 다음과 같이 입력한다
```
#!/usr/bin/zsh
MODE='DEBUG' ./manage.py $1
```
`$1`는 위치인자의 첫번째를 가리킨다. `$*`로 설정하면 모든 위치인자를 순서대로 넘겨 받는다.

> 자신의 zsh경로는 which zsh로 확인할 수 있다.

#### 권한변경
```
chmod 755 manage
```

.zshrc 적용
```python
vi ~/.zshrc
export SCRIPTS_PATH="$HOME/.scripts"
export PATH="$PATH:$SCRIPTS_PATH"

source ~/.zshrc
```

manage를 실행해서 다음과 같이 나오면 정상작동한다고 볼 수 있다. ./manage.py가 있는 디렉토리에서 실행하면 제대로 되기 때문. 
```
/.scripts/manage
/home/lewis/.scripts/manage:2: no such file or directory: ./manage.py
```

## static 폴더 추가하기

우선 settings.py에 static 관련 설정을 해준다. 

```python
# static settings
STATIC_ROOT = os.path.join(ROOT_DIR, 'static_root')
STATIC_DIR = os.path.join(BASE_DIR, 'static')
STATIC_URL = '/static/'
STATICFILES_DIRS = (
    STATIC_DIR,
)
```
static 설정하고 나서 전체 내용을 서버로 전송한다.  
django 내용만 바뀌었기 때문에 sudo systemctl restart uwsgi만 해도 된다.

![0308-15](https://s8.postimg.org/lrpip5ulx/0308_15.png)

설정이 완벽히 하지 않았기 때문에 그림 파일이 제대로 나오지 않을 것이다.

![0308-16](https://s8.postimg.org/6x0xazl11/0308_16.png)

관리자페이지를 들어가도 css가 적용되지 않은 것을 확인할 수 있다. 로컬 프로젝트에서 External Libraries의 site-packages/django/contrib/admin/static/admin경로로 가면 css, fonts, img, js 폴더가 있다. 로컬의 장고프로젝트는 이 경로에서 css를 비롯한 static 정보를 기본값으로 사용한다. 

static 파일 경로가 여러개 존재하는데 지금 상태로는 STATICFILES_DIRS에 지정한 폴더와(따로 추가했기 때문) django.contrib.admin.static 폴더 2개를 찾는다. 우리가 해야할 작업은 이 두 폴더를 한 곳으로 미리 모아두어야 한다. 모아주는 명령어는 collectstatic이다. 

```
manage collectstatic
```

다음과 같이 static_root에 admin(django.contrib.admin)과 images(static/images)이 추가된 것을 확인할 수 있다. 

![0308-17](https://s8.postimg.org/3rgbks2et/0308_17.png)

.gitignore파일에 static_root/를 추가한다. 

업로드된 정보를 서버로 전송한다.   

### 서버에서 static 설정하기

서버에서는 sudo systemctl restart uwsgi로 전송내역 반영한다. 서버에서 ./manage.py collectstatic하면 미리 되어있다는 내용이 출력될 것이다. 

여기까지 해도 서버 주소를 들어가면 사진이 나오지 않는다. 그 이유는 nginx는 static파일에 접근할 때 특정 경로를 찾아가야하는데 관련 설정을 해주지 않았기 때문이다. nginx에 static location내용을 추가한다.

> etc/nginx/sites-available/app

```python
sudo vi app

location /static/ {
    alias /srv/app/static_root/;
}
```
sudo systemctl restart nginx 한 다음 서버주소로 들어가서 사진이 나와야 한다.




```











```
