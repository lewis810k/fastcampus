# docker(2)

- uwsgi 설정 
- 로그 확인하기
- nginx 설정

### uwsgi 설정  

runserver는 확인했으므로 uwsgi를 테스트해본다.

현재는 COPY명령어로 현재 폴더의 내용을 다 복사하는 상태다. **COPY명령어와 경로를 잘 사용하면 ubuntu 서버에서 uswgi 설정하는 경로에 설정파일을 넣을 수 있다.** 

편의상 이미 작성해두었던 deploy-ec2 서버의 app.ini 내용을 복사해온다.  

현재 프로젝트에서 uwsgi-app.ini를 생성한다.

> .conf/uwsgi-app.ini

```
[uwsgi]
chdir = /srv/app/django_app
module = config.wsgi:application

;uid = www-data
;gid = www-data

socket = /tmp/app.sock
chmod-socket = 666
;chown-socket = www-data:www-data

enable-threads = true
master = true
vacuum = true
pidfile = /tmp/app.pid

logger = file:/tmp/uwsgi.log
logger = internalservererror file:/tmp/uwsgi500.log
```
app 이름을 config로 변경했기 때문에 module에서 config.uwsgi로 변경한다. 

uid, gid, chown-socket은 일단 주석처리한다. 

위의 설정파일을 우리가 원하는 경로로 넣어준다. 이 작업은 Dockerfile에서 처리한다. 

> Dockerfile

```
FROM        eb-base
MAINTAINER  dev@azelf.com

COPY		. /srv/app		#현재경로를 통째로 복사
WOKRDIR		/srv/app		#WORKDIR를 복사한 디렉토리로 바꿔준다.

RUN			pip3 install -r requirements.txt	#pip3를 이용하여 패키지 설치
RUN			pip3 install uwsgi

;추가된 작업
COPY		.conf/uwsgi-app.ini	/etc/uwsgi/sites/app.ini
```
추가된 작업은, 우선 방금 만든 uwsgi-app.ini를 서버에서 app.ini파일로 복사하는데 위의 경로를 이용한다. 

runserver는 정상작동을 확인했기 때문에 제거해준다.

-

서버실행
```
docker run --rm it -p 4040:8080 eb
```

uwsig로 서버를 켜고 이 때 포트를 8080으로 지정한다. 
```
uwsgi --http :8080 --chdir /srv/app/django_app/ -w config.wsgi
```

이제 `localhost:4040`으로 접속하면 정상작동 해야한다. 

-

이번에는 직접 포트를 지정하지 않고 ini 파일을 이용해서 열어본다. 
```
uwsgi --http :8080 -i /etc/uwsgi/sites/app.ini
```

이번에도 `localhost:4040`으로 접속해서 정상작동을 확인한다. uwsgi로 실행하고 있기 때문에 더이상 명령어를 입력할 수 없는 상태가 된다. 이 서버에 대한 작업을 진행하고 싶다면 현재 실행되고 있는 docker container에 접속해서 작업을 진행한다.

-

### 로그 확인하기

docker ps를 치면 현재 실행되고 있는 컨테이너 목록이 뜬다. PORTS에서 4040 포트로 연결된 곳을 찾고 container_id로 접근해본다. 

```
docker exec -it container_id /bin/bash
```
컨테이너에 bash shell로 접속한다. `-it`는 도커 접속 후 입력작업을 위한 옵션

app.ini 파일에 보면 `logger = file:/tmp/uwsgi.log`가 있다. uwsgi에 대한 로그가 이 파일에 저장된다. 로그를 확인해본다.

```
cd /tmp
cat uwsgi.log
```
로그정보가 출력된다. 페이지를 새로고침하고 다시 로그정보를 출력해보면 추가정보가 입력된 것을 확인할 수 있다. (시간만 비교해봐도 추가된 로그를 쉽게 확인할 수 있다.)  


-

### nginx 설정

nginx 파일은 deploy-ec2 서버에서 다음 경로에 있는 nginx 파일들을 가져온다.

```
/etc/nginx/nginx.conf
/etc/nginx/sites-available/app
```
내용을 붙여넣기 해준 다음 필요에 맞게 수정한다.

> .conf/nginx.conf

```
user root;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
    ...
...
```
지금은 일단 root로 구동할텐데, 나중에 www-data로 구동하는 방법도 직접 해보길.

> .conf/nginx-app.conf

```
server {
	listen 80;
	server_name localhost ec2-13-124-15-182.ap-northeast-2.compute.amazonaws.com ec2.coolmong.com;
	charset utf-8;
	client_max_body_size 128M;


	location / {
		uwsgi_pass    unix:///tmp/app.sock;
		include       uwsgi_params;
	}
}
```
가져와서 루트경로에 대한 설정만 남겨두고 나머지는 삭제한다. server_name은 자신의 서버주소이어야 한다. 기존에 있던 static은 AWS에서 가져올 예정이기 때문에 나중에 다시 설정할 예정이다.

이제 nginx 파일 2개를 원하는 위치로 복사해야한다. 복사하는 방법은 uwsgi를 COPY 명령어로 처리해준 것과 동일하다. 그리고 심볼릭링크를 설정한다.

> Dockerfile

```
...

COPY		.conf/nginx.conf		/etc/nginx/nginx.conf
COPY		.conf/nginx-app.conf	/etc/nginx/sites-available/app.conf
RUN         ln -s /etc/nginx/sites-available/app.conf   /etc/nginx/sites-enabled/app.conf
```
자세히 보면 로컬의 nginx-app.conf를 복사할 때 app.conf로 이름을 바꾼다. 그리고 바뀐 이름을 가지고 링크를 설정한다.

다시 빌드

```
Dokcer build . -t eb
```

#### 테스트

테스트를 위해서 nginx-app.conf 파일의 `listen 80;`를 `listen 4040;`으로 변경한다.

docker를 실행한다.
```
docker run --rm -it -p 5050:4040 eb
```

nginx와 uwsgi를 직접실행한다. 
```
nginx
uwsgi --ini /etc/uwsgi/sites/app.ini
```

>외부에 열리는 포트는 5050이다.`(localhost:5050)` 	
5050을 통해 docker내부에서 4040으로 연결된다.  
해당 4040포트는 nginx 요청을 받고 있다.  
이 요청을 받으면 nginx가 app.sock을 통해 uwsgi 요청을 준다.  
해당 uwsgi가 django application을 돌리고 있는 상태이다.  

따라서, `localhost:5050`으로 접속할 경우 정상페이지가 출력되어야한다.

어떤 OS에서든 docker를 설치할 수 있으면 배포환경을 똑같이 유지시켜줄 수 있다는 장점이 있다. 

-

#### suvervisord

이렇게 실행했는데 nginx나 uwsgi가 어떤 오류로 인해 terminate될 경우 문제가 발생한다. 관리해주기 위해서 supervisord를 사용한다.

supervisord으로 실행하면 백그라운드에서 데몬으로 돌면서 지정한 프로그램이 죽을 경우 다시 살려준다. 

supervisor-app.conf파일을 새로 만든다. 

> .conf/supervisor-app.conf

```
[program:uwsgi]
command = uwsgi --ini /etc/uwsgi/sites/app.ini

[program:nginx]
command = nginx
```

Dockerfile base에서 supervisor를 설치하는 명령어를 입력한다. 

> _Dockerfile_base

```
...
RUN         apt-get -y install nginx
RUN         apt-get -y install supervisor	#추가

RUN         pip3 install django
...
```
내용이 수정되었기 때문에 eb-base 이미지를 다시 만든다. 

```
docker build . -t eb-base -f _Dockerfile_base
```

Dockerfile에 supervisor도 작동할 수 있도록 설정한다.

> Dockerfile

```
...
COPY        .conf/nginx-app.conf        /etc/nginx/sites-available/app.conf
COPY        .conf/supervisor-app.conf   /etc/supervisor/conf.d/		#추가
RUN         ln -s /etc/nginx/sites-available/app.conf   /etc/nginx/sites-enabled/app.conf


EXPOSE      4040				#추가
CMD         supervisord -n		#추가
```
supervisorp-app.conf 파일을 docker안에서도 복사되도록 해주고, 밖으로 열릴 포트를 설정한다. eb를 다시 빌드한다. 
```
docker build . -t eb
```

잘 완료됐다면 실행해보자. 
```
docker run --rm -it -p 5050:4040 eb
```
잘 실행이 되지 않을 것이다. 

supervisord가 실행하는 프로그램이 daemon형태로 들어가면 감지를 하지 못한다. 해제해야한다. 

> .conf/nginx.conf

```
...
include /etc/nginx/modules-enabled/*.conf;
daemon off;		#추가

...
```
백그라운드에서 돌던 nginx가 포어그라운드에서 돌게 된다. supervisord가 어떤 프로세스를 자신이 관리하려면 포어그라운드에 잡혀있어야 한다. nginx가 돌아가는 모드가 daemon이 아니라 shell prompt상에서 돌게하는 것이다. 

다시 빌드하고 실행한다.
```
docker build . -t eb
docker run --rm -it -p 5050:4040 eb
```
success 후에 에러가 나지 않고 RUNNING state를 유지해야한다.

그리고 밖에서 `localhost:5050`으로 접속해서 정상작동을 확인한다. 

