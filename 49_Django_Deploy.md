# Ubuntu Linux Deploy

기본 내용은 [Ubuntu Linux Deploy](https://github.com/LeeHanYeong/Fastcampus-WPS-4th/blob/master/Deploy/01.%20EC2.md)를 참고하고 부가설명를 추가한다. 

> AWS EC2 + Ubuntu16.04 + Nginx + uWSGI + Django

### AWS EC2 Instance생성

[AWS 계정 생성하기](http://pyrasis.com/book/TheArtOfAmazonWebServices/Chapter03)

![0306-1](https://s3.postimg.org/kiu9io6pf/0306_1.png)

메인 페이지의 우측 상단에 지역을 Seoul로 설정한다. 

![0306-2](https://s3.postimg.org/hd9nsgo37/0306_2.png)

좌측 상단에서 Service 탭을 누르고 EC2를 누르면 EC2 대쉬보드로 이동한다. 

![0306-3](https://s16.postimg.org/u27dvh811/0306_3.png)

화면 가운데 있는 Launch Instance 버튼을 클릭한다. 

![0306-4](https://s4.postimg.org/wejv82dwt/0306_4.png)

Ubuntu Server를 선택한다.

![0306-5](https://s4.postimg.org/kqptdiorx/0306_5.png)

무료로 제공되는 t2.micro type을 선택하고 launch 한다. launch를 하고 security group을 수정한다.

![0306-6](https://s4.postimg.org/c9qb2lk31/0306_6.png)

이 페이지에서는 security group 이름을 설정할 수 있다. 좌측 하단에 Add Rule 버튼을 누르고 위와 같이 Custom TCP Rule을 추가한다. 

인스턴스를 생성하면 암호화키에 대한 창이 뜬다. 기존에 가지고 있는 키가 있다면 선택하고 인스턴스를 처음 생성했다면 새로운 키를 만들고 다운로드 받는다. **키는 반복해서 다운로드되지 않기 때문에 보관에 유의해야한다.**

![0306-7](https://s2.postimg.org/whppus37d/0306_7.png)

새로운 인스턴스가 생성되었고, running 상태에 있다. 

#### Key Pair

인스턴스 생성시 만들었던 .pem 파일을 다음 경로에 저장한다. 
```
/home/lewis/.ssh/lewis.pem
```
경로의 lewis는 각자 PC 이름에 해당하고 lewis.pem의 lewis는 pem의 이름이다. 

pem파일에 접근할 때 다음과 같은 에러가 날 수 있다. 
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for '/Users/Arcanelux/.ssh/fastcampus.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/home/lewis/.ssh/lewis.pem": bad permissions
Permission denied (publickey).
```
현재 소유주만 읽을 수 있도록 권한을 수정한다. 
```
chmod 400 lewis.pem
```

-

### Ubuntu 기본 설정

pem 파일과 서버주소를 통해 서버에 접속한다.

![0306-08](https://s21.postimg.org/u2nvky3hz/0306_8.png)

#### 접속 명령어
```
ssh -i ~/.ssh/lewis.pem ubuntu@(EC2 인스턴스의 Public DNS)
```
yes를 입력하고 서버에 접속한다. 

#### 언어팩 설치
```
sudo apt-get install language-pack-ko
sudo locale-gen ko_KR.UTF-8
```

#### python-pip설치
```
sudo apt-get update

sudo apt-get install python-pip
```
update를 먼저 실행하지 않으면 python-pip가 제대로 설치되지 않을 것이다. 


#### zsh 설치
```
sudo apt-get install zsh
```

#### oh-my-zsh 설치
```
sudo curl -L http://install.ohmyz.sh | sh
```

#### Default shell 변경
```
sudo chsh ubuntu -s /usr/bin/zsh
```

#### pyenv requirements설치
```
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils
```

#### pyenv 설치
```
curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
```
pyenv 설치시 경로를 추가하라는 경고메시지가 출력된다. 

#### pyenv 설정 .zshrc에 기록
```
vi ~/.zshrc

export PATH="/home/ubuntu/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```
![0306-9](https://s22.postimg.org/4gao9i5ch/0306_9.png)

#### Pillow 라이브러리 설치
```
sudo apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev libfreetype6-dev liblcms2-dev libwebp-dev tcl8.6-dev tk8.6-dev python-tk
```

이쯤에서 터미널을 통한 서버 접속을 끊고 재접속을 해서 모든 변경사항이 적용되도록 한다. 

-

### Django 관련 설정

서버의 srv/ 경로에 app이라는 디렉토리를 생성하려고 하는데 권한때문에 에러가 발생하기 때문에 디렉토리 권한을 재설정한다. 
```
sudo chown -R ubuntu:ubuntu /srv/
```
![0306-11](https://s21.postimg.org/daiue4q1j/0306_12.png)
srv의 사용자가 root에서 ubuntu로 바뀐 것을 확인할 수 있다. 

새로운 유저를 등록할 때는 다음과 같은 명령어를 사용한다. 
```
sudo adduser ubuntu
```
app 디렉토리가 생성되었으니 로컬에 있던 프로젝트를 서버로 올려보자. 

![0306-13](https://s21.postimg.org/x6cpqz387/0306_13.png)
```
-r 					: 폴더 작업 관련 명령일 때 사용
-i ~/.ssh/lewis.pem : 키 전송
. 					: 현재경로
내용: 현재 디렉토리의 내용을 서버의 /srv/app 폴더로 복사한다. 
```
서버의 /srv/app/경로에 프로젝트가 잘 복사됐는지 확인한다. 


#### pyenv 3.5.2설치 및 virtualenv생성
```
pyenv install 3.5.2
pyenv virtualenv deploy_ec2
pyenv local deploy_ec2
```

#### requirements설치
```
pip install -r requirements.txt
```

#### runserver 테스트
```
cd django_app
./manage.py runserver 0.0.0.0:8080
```
Django가 제대로 설치되지 않았다는 에러가 발생한다면 pip list를 통해 설치된 목록을 확인한다. django가 확인되는데 에러가 계속 발생한다면 재접속을 하고 다시 pip list를 해보면 설치가 안 되어있는 것을 확인할 수 있다. deploy_ec2 바깥에 Django가 설치되었다면 pip uninstall django 명령어로 삭제한다. 그리고 app 디렉토리 안에서 pip install django 명령어를 입력한다. 

EC2 인스턴스의 `public IP:8080`으로 접속해서 테스트한다. 

#### AWS Security Groups 8080 Port추가

8080 포트에 대한 접근권한이 열려있지 않다면 Security Groups에서 추가해준다. 
```
Security Groups -> Inbound -> Edit -> Custom TCP Rule -> 8080
```

#### ALLOWED_HOSTS 설정
```
vi mysite/settings.py
ALLOWED_HOSTS = [
    '<ec2 domain name'>,
    또는
    '.amazonaws.com',
]
```
> 이거 안 해도 돌아감??

-

### uWSGI 관련 설정

#### 웹 서버 관리용 유저 생성
```
sudo adduser nginx
```

#### uWSGI설치
```
(virtualenv 환경 내부에서)
pip install uwsgi
```

#### uWSGI 정상 동작 확인
```
uwsgi --http :8080 --home (virtualenv경로) --chdir (django프로젝트 경로) -w (프로젝트명).wsgi
```

ex) pyenv virtualenv이름이 deploy_ec2, django프로젝트가 /srv/app/django_app, 프로젝트명이 app일 경우
```
uwsgi --http :8080 --home ~/.pyenv/versions/deploy_ec2 --chdir /srv/app/django_app -w deploy_ec2.wsgi
```
실행 후 :8080으로 접속하여 요청을 잘 받는지 확인

#### uWSGI 사이트 파일 작성
[uwsgi]부터 다 입력해야한다.
```
sudo mkdir /etc/uwsgi
sudo mkdir /etc/uwsgi/sites
sudo vi /etc/uwsgi/sites/app.ini

[uwsgi]
chdir = /srv/app/django_app
module = deploy_ec2.wsgi:application
home = /home/ubuntu/.pyenv/versions/deploy_ec2

uid = www-data
gid = www-data

socket = /tmp/app.sock
chmod-socket = 666
chown-socket = www-data:www-data

enable-threads = true
master = true
pidfile = /tmp/app.pid
```


#### uWSGI site파일로 정상 동작 확인
```
uwsgi --http :8080 -i /etc/uwsgi/sites/app.ini
```
#### sudo로 root권한으로 실행
```
sudo /home/ubuntu/.pyenv/versions/deploy_ec2/bin/uwsgi --http :8080 -i /etc/uwsgi/sites/app.ini
```

#### uWSGI 서비스 설정파일 작성
```
sudo vi /etc/systemd/system/uwsgi.service

[Unit]
Description=uWSGI Emperor service
After=syslog.target

[Service]
ExecPre=/bin/sh -c 'mkdir -p /run/uwsgi; chown www-data:www-data /run/uwsgi'
ExecStart=/home/ubuntu/.pyenv/versions/deploy_ec2/bin/uwsgi --uid www-data --gid www-data --master --emperor /etc/uwsgi/sites

Restart=always
KillSignal=SIGQUIT
Type=notify
StandardError=syslog
NotifyAccess=all

[Install]
WantedBy=multi-user.target
```



