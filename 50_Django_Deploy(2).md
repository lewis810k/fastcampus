# Django Deploy(2)
- 키 관리
- nginx 설정
- 도메인 연결
- IAM 유저 생성

### 키 관리

![0307-4](https://s9.postimg.org/qvv2ph0q7/0307_4.png)

IAM service를 클릭한다. IAM의 좌측 Users탭에서 adduser를 클릭

![0307-5](https://s9.postimg.org/kw7bltfxr/0307_5.png)

원하는 username을 입력한다. Access type에서 **Programmatic access**를 클릭한다. 내용을 보면 개발환경에 대한 access key ID와 secret access key를 활성화시킨다고 되어있다. Next를 누르면 유저가 생성되고 키를 제공받는다.

![0307-1](https://s27.postimg.org/tvy50n2ub/0307_1.png)

local상에서 pip install awscli 한 다음 configure 세팅을 해준다.

![0307-2](https://s27.postimg.org/wrb87i6ub/0307_2.png)

.aws 디렉토리에 config, credentials 파일이 생긴다.

![0307-3](https://s27.postimg.org/8c306g7xf/0307_3.png)

키를 제외한 내용들이 config 파일에 저장되어 있는 것을 확인할 수 있다. credentials 파일에 키 정보가 저장된다. 

-

### Nginx 관련 설정

#### Nginx 안정화 최신버전 사전세팅 및 설치
```
sudo apt-get install software-properties-common python-software-properties
sudo add-apt-repository ppa:nginx/stable
sudo apt-get update
sudo apt-get install nginx
nginx -v
```

#### Nginx 동작 User 변경
```
sudo vi /etc/nginx/nginx.conf

user nginx;
```

/etc/nginx/nginx.conf 파일을 sudo 권한으로 열어서 서버이름 길이제한을 늘려준다. 
```
server_names_hash_bucket_size 512;
```

#### Nginx 가상서버 설정 파일 작성

설정 파일을 수정하기 전에 초기화 세팅이 잘 되는지 확인해보자. 디폴트 정보는 다음 경로에 저장되어 있다. 

>/etc/nginx/sites-available/dafault

default 파일을 열어보면 다음과 같은 구문이 있다.
```
listen 80 default_server;
```
80번 포트로 들어오는 요청을 처리해준다. 

80번 포트로 수신이 되었을 경우 다음 동작을 수행한다. 
```
root /var/www/html;

index index.html index.htm index.nginx-debian.html
```
root로 설정되어있는 디렉토리에서 index의 내용 중 하나라도 해당되는 것이 있다면 실행해준다. 이 상태에서 ec2의 `ip주소:80`을 입력했을 때 다음과 같은 화면이 나오면 정상적으로 작동하는 것이다. 
![0307-6](https://s2.postimg.org/vpp9rm4yx/0307_6.png)

그렇다면 실제로 root 경로의 html 디렉토리 내부를 살펴보자. html 디렉토리에서 ls 명령어를 실행하면 index.nginx-debian.html 파일을 확인할 수 있다. (현재 작업환경 기준)

![0307-7](https://s16.postimg.org/r0euqjb3p/0307_7.png)

html파일 내부에 위와 같은 코드들이 작성되어 있고 이 내용을 수정할 경우 디폴트 화면의 내용이 바뀐다. 

기본화면이 잘 출력되는 것으로 보아 가상서버는 잘 연동되었다. 

이제 디폴트 구성이 아닌 AWS EC2 인스턴스를 연결하는 작업을 해보자. 우선 default의 내용을 더이상 사용하지 않고 app이라는 파일을 새로 생성하고 다음 코드를 입력한다.

```
sudo vi /etc/nginx/sites-available/app

server {
    listen 80;
    server_name localhost ec2-13-124-15-182.ap-northeast
    -2.compute.amazonaws.com;					#1
    charset utf-8;
    client_max_body_size 128M;


    location / {
        uwsgi_pass    unix:///tmp/app.sock;		#2
        include       uwsgi_params;
    }
}
```
`#1`: localhost 뒤에 접근을 허용할 EC2 주소를 추가한다.  
`#2`: '/' URL로 들어올 경우 tmp/app.sock을 통해 uWSGI의 app.sock으로 연결된다. uswgi는 etc/uwsgi/sites/app.ini 파일의 내용을 이용하여 장고정보를 불러온다. app.ini내에 지정한 socket 이름과(app.sock) 동일하게 설정한다. 

>etc/uwsgi/sites/app.ini

```python
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
vacuum = true
pidfile = /tmp/app.pid
```
chdir는 장고와 연결되어 있는 디렉토리 정보를 가지고 있다. module에는 project의 wsgi가 등록되어 있다. 결국, chdir에 등록되어있는 경로를 찾아가서 deploy_ec2.wsgi정보를 uswgi로 반환해준다. uswgi는 소켓을 통해 요청이 들어온 곳으로 장고정보를 실어서 보내준다. 


#### 설정파일 심볼릭 링크 생성
```
sudo ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled/app
```
![0308-1](https://s11.postimg.org/rpvkh8q1f/0308_1.png)

app에 대한 링크가 추가된 것을 확인할 수 있다. nginx가 요청을 받을 때 해당 요청에 대한 처리를 sites-enabled에서 한다. 
>링크하는 이유를 정확히 잘 모르겠음.. 

-

### 도메인 설정하기 (추가 예정)
- hosting.kr
- www.cloudflare.com

cloudflare -> Page Rules -> https 설정  
cloudflare -> DNS -> CNAME 설정

![0309-7](https://s30.postimg.org/grtwmhv4h/0309_7.png)

-

### IAM 유저생성
```
1. AWS에서 IAM service를 클릭
2. 좌측에 있는 Users 탭 클릭
3. adduser
```

![0309-2](https://s9.postimg.org/e9ejttd1r/0309_2.png)  
```
4. 원하는 username 입력
5. Access type에서 Programmatic access 클릭한다. 내용을 보면 개발환경에 대한 access key ID와 secret access key를 활성화시킨다고 되어있다. 
6. Next를 누르면 유저가 생성되고 키를 제공받는다.
```
![0309-3](https://s9.postimg.org/aeb5r8tvz/0309_3.png)
```
7. pip install awscli
8. aws configure
9. Access Key ID, Access Key, region(ap-northeast-2), output format(json) 입력
```
![0309-4](https://s9.postimg.org/s5ms5p9an/0309_4.png)
```
10. ~/.aws 디렉토리에 config 파일이 생긴다.
```
![0309-5](https://s9.postimg.org/b6dtqfy33/0309_5.png)
```
11. 키를 제외한 내용들이 config파일에 저장되어 있는 것을 확인할 수 있다. 키는 credentials에 저장된다.
```
