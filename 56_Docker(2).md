# docker(2)

- uwsgi 테스트 
- 로그 확인하기

### uwsgi 테스트  

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











```
