# docker(1)

[docker 알아보기](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)


### Django Project 세팅

만약 project 디렉토리에서 django-admin startproject eb 명령어로 프로젝트를 생성했다면 project/eb/eb형태로 디렉토리들이 생성된다. 이 설정을 project/django_app/config로 변경한다.

config로 변경하고 runserver를 하면 에러가 발생한다. 기존의 eb라는 이름이 여러 곳에서 참조되고 있기 때문이다. 해당 부분을 모두 찾아 config로 변경한다. eb를 마우스 우클릭하고 refactor -> rename 으로 변경하면 참조된 곳 모두 한번에 변경된다.

-

### RUN 명령어의 옵션

옵션|설명
---|---
-d|detached mode 흔히 말하는 백그라운드 모드
-p|호스트와 컨테이너의 포트를 연결 (포워딩)
-v|호스트와 컨테이너의 디렉토리를 연결 (마운트)
-e|컨테이너 내에서 사용할 환경변수 설정
–name|컨테이너 이름 설정
–rm|프로세스 종료시 컨테이너 자동 제거
-it|-i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션
–link|컨테이너 연결 [컨테이너명:별칭]

-

### docker안에서 하나씩 설치하는 방법
```
docker images
```
만약 ubuntu가 없으면 추가해준다.
```
docker pull ubuntu:16.04
```
컨테이너를 생성하고 서버에서 했던 세팅을 컨테이너에도 해준다. 


#### apt-get 필수 패키지 설치
```
docker run --rm -it ubuntu:16.04 /bin/bash

apt-get update
apt-get install python3

도커에서는 가상환경을 쓸 필요가 없기 때문에 쓰지 않음.

apt-get install python3-pip
apt-get install nginx
```

#### 프로젝트 생성
```
cd /srv
mkdir app
cd app
pip3 install django		#python3를 사용하려면 pip3를 써야한다.
pip3 install uwsgi

django-admin startproject eb
```
프로젝트르 생성하고 python3 manage.py runserver 하면 잘 작동하지만 해당 서버에 접근할 방법이 없다는 문제가 발생한다. 그 이유는 컨테이너를 열었지만 **외부포트를 열어주지 않아서** 연결되지 않고 그냥 독립적인 상태에 있다.

여기서 컨테이너를 종료하고 다시 docker run하면 기존에 설치했던 정보들이 없어진 상태이다. docker 실행 시 이미지는 고정된 상태로 있지만 컨테이너가 종료되면 사용했던 내용들이 사라진다. 

그래서 docker를 실행할 때마다 매번 실행할 수 없기 때문에 미리 Dockerfile이라는 곳에 입력한다.  

-

### Dockerfile
Dockerfile은 한번 실행하면 내부 명령어를 모두 실행한다. 최상위 디렉토리에서 파일을 생성한다. 만약 Docker 타입이 없다면 Docker integration plugin을 추가한다. 

> deploy_eb_dokcer/_Dockerfile_base

.gitignore에 `_Dockerfile*`을 추가한다. 

```
FROM        ubuntu:16.04
MAINTAINER  dev@azelf.com

RUN         apt-get -y update
RUN         apt-get -y install python3
RUN         apt-get -y install python3-pip
RUN         apt-get -y install nginx
```
전체 스크립트가 실행되면서 명령어가 단계적으로 수행되는데 yes/no를 입력해야하는 상황이 오면 실행을 중단해버린다. `-y`은 이러한 상황에 대해 항상 y를 입력해준다. 

docker는 이미 존재하는 이미지 위에 다른 작업을 하면 덮어씌워서 새로운 이미지를 생성한다. 우리가 필요한 apt-get 패키지를 base를 이용하여 이미지를 미리 만든다. 그리고 이 이미지를 기반으로 작업을 진행한다.
```
docker build . -t eb-base -f Dockerfile_base
(현재위치에서 Dockerfile_base라는 file을 기준으로 eb-base를 생성한다.)
```

이 상황에서 docker images를 쳐보면 eb-base가 생성된 것을 확인할 수 있다. 이 이미지를 기준으로 작업을 하기 때문에 아래의 Dockerfile의 FROM이 eb-base인 것이다.

> Dockerfile

```
FROM        eb-base
MAINTAINER  dev@azelf.com


COPY		. /srv/app		#현재경로를 통째로 복사
WOKRDIR		/srv/app		#WORKDIR를 복사한 디렉토리로 바꿔준다.
```

eb 이미지를 생성한다.
```
docker build -t eb .
```

docker images로 확인한다.
```
REPOSITORY	TAG		IMAGE ID		CREATED			SIZE
eb          latest  c64c6b8b6a18    4 seconds ago   510 MB
eb-base     latest  1e08ad9fcfdb    22 minutes ago  510 MB
```

```
docker run -p 4567:8080 eb
```
`localhost:4567`로 접속해서 정상작동하는지 확인한다.

COPY        . /srv/app
WORKDIR     /srv/app

RUN         pip3 install -r requirements.txt
RUN         pip3 install uwsgi
WORKDIR     /srv/app/django_app
CMD         ["python3", "manage.py", "runserver", "0:8080"]


-

실제 서버에서 돌아가고 있는 것을 신경쓰지 않고 도커만 생성하면 원하는 운영체제의 product를 만들 수 있다.

-

### docker 이미지 삭제


docker rmi docker_image_id

이름이 none으로 되어있는 이미지 삭제
docker rmi -f $(docker images --filter "dangling=true" -q)

```
container 중지 및 삭제
```

-

### deploy_ec2 프로젝트와 docker 연결

runserver는 확인했으므로 uwsgi를 테스트해본다.

Dockerfile의 COPY명령어를 잘 사용하면 uswgi의 설정경로로 바로 접근할 수 있다.

서버에 있는 app.ini의 내용을 복사해온다. 

deploy_ec2 프로젝트에서 uwsgi-app.ini를 생성한다.

> .conf-secret/uwsgi-app.ini

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
module에서 config로 이름변경하고 uid, gid, chown-socket을 주석처리한다.







django_app 안에 폴더 config로 바꿀 때 연결된거 모두 바꾸는 명령어로.  
.gitignore에 `_Dockerfile*`추가


nginx.conf
user root;
daemon off;
```
