# Django Deploy(5)

- RDS 인스턴스 생성
- Security Group 설정
- pgAdmin에서 RDS 접속
- RDS로 migrate


[RDS란 무엇인가?](http://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/Welcome.html)

### RDS 인스턴스 생성

1. AWS service에서 RDS를 선택한다.
2. Launch DB Instance
3. Postgresql 선택
4. Dev/Test 선택
5. DB Instance Class: t2.micro, Multi-AZ Deployment: No
6. settings에 정보를 적절히 입력하고 next를 누른다. 
7. VPC Security Group(s): create new security group, DB Name 설정 후 Launch

-

### Security Group 설정

방금 생성된 RDS 인스턴스의 Security Group은 새로 만들어진 group으로 설정되어있다. 보통 rds_launch_wizard라는 group으로 설정된다. 나중에 여러 group이 생성되면 group name 때문에 혼동이 올 수 있기 때문에 미리 정리를 잘해둔다. 

rds_launch_wizard를 클릭하고 action -> copy to new, 그리고 원하는 이름을 설정한다. rds_launch_wizard를 삭제하려고 하면 associate 되어있다는 메시지와 함께 삭제가 불가능할 것이다. RDS 인스턴스로 돌아간다. 

Instance Action -> Modify

![0309-9](https://s8.postimg.org/9d4awrvsl/0309_9.png)

Security Group 박스에서 새롭게 생성했던 group만 선택하고 저장한다. 

rds_launch_wizard를 삭제한다.

#### inbound 추가

FastCampus EC2 Deploy RDS의 inbound에서 rule을 추가한다.  

![0309-10](https://s3.postimg.org/owut2i1dv/0309_10.png)

위와같이 설정하고 마지막 Source는 MyIP를 선택한다. 

-

### pgAdmin에서 RDS에서 접속

로컬에서 pgAdmin 가상환경이 세팅된 경로로 가서 pgAdmin을 연다. 
```
python ~/.pyenv/versions/3.5.2/envs/pgadmin_env/lib/python3.5/site-packages/pgadmin4/pgAdmin4.py
```
`localhost:5050` 접속!

어느 경로에서든 명령어가 동작하게 하려면 zsh alias를 설정한다. (추가예정)

새로운 서버를 생성한다. 

![0309-12](https://s17.postimg.org/a3uboeo6n/0309_12.png)

-

![0309-11](https://s17.postimg.org/olriwefhr/0309_11.png)

connection 탭에서 host name은 RDS 인스턴스의 end point 주소를 사용한다. 이 때 마지막 포트번호는 제외하고 `.com`으로 끝나도록 한다. 

나머지 정보도 RDS 생성시 입력한 정보와 동일하게 작성하고 SAVE를 누른다. 

-

### RDS로 migrate

> settings.py

```python
DB_RDS = os.environ.get('DB') == 'RDS' 

...
if DEBUG and DB_RDS:
    # DEBUG 모드이며 DB_RDS 옵션일 경우, 로컬 postgreSQL이 아닌 RDS로 접속해 테스트한다.
    config_db = config['db_rds']
else:
    # 그 외의 경우에는 해당 db설정을 따름
    config_db = config['db']

DATABASES = {
    'default': {
        'ENGINE': config_db['engine'],
        'NAME': config_db['name'],
        'USER': config_db['user'],
        'PASSWORD': config_db['password'],
        'HOST': config_db['host'],
        'PORT': config_db['port'],
    }
}
```
RDS가 True이면 DB_RDS에 연결하여 데이터를 불러온다. 

deploy.json과 local.json의 현재 db정보 상태는 다음과 같다. 

> settings_deploy.json

```python
  "db": {
    "engine": "django.db.backends.postgresql_psycopg2",
    "name": "deploy_ec2",
    "user": "lewis_db",
    "password": "--------",
    "host": "deploy-db.ca6ihz3f4f12.ap-northeast-2.rds.amazonaws.com",
    "port": "5432"
  }
```

> settings_local.json

```python
  "db": {
    "engine": "django.db.backends.postgresql_psycopg2",
    "name": "deploy_ec2",
    "user": "lewis",
    "password": "--------",
    "host": "localhost",
    "port": "5432"
  },
  "db_rds": {
    "engine": "django.db.backends.postgresql_psycopg2",
    "name": "deploy_ec2",
    "user": "lewis_db",
    "password": "--------",
    "host": "deploy-db.ca6ihz3f4f12.ap-northeast-2.rds.amazonaws.com",
    "port": "5432"
  }
```

실행! 
```
MODE='DEBUG' STORAGE='S3' DB='RDS' ./manage.py runserver
```
`localhost:8000/admin`으로 접속하면 에러가 발생한다. RDS에 migrate가 되지 않았기 때문이다. 

migrate!
```
MODE='DEBUG' STORAGE='S3' DB='RDS' ./manage.py migrate
```
다시 실행!
```
MODE='DEBUG' STORAGE='S3' DB='RDS' ./manage.py runserver
```

admin 페이지가 정상적으로 나올 것이다. 그리고 pgAdmin 에서도 `table`이 다음과 같이 추가된 것을 확인할 수 있다. 

![0309-13](https://s18.postimg.org/3k3y1cmp5/0309_13.png)


-

이제 deploy에서는 항상 rds_db가 설정된다. 서버에서 postgresql을 더이상 사용하지 않기 때문에 삭제한다. 
```
sudo apt-get remove postgresql
sudo apt-get autoremove
```
로컬데이터를 서버로 deploy

관리자페이지에서 체크해보면 시간초과로 인해(504 Error) 데이터베이스로 접속을 못할 것이다.

RDS의 security group을 보면 inbound가 MyIP로만 설정되어있기 때문이 내 주소만 허용되기 때문이다. EC2에 설정된 security group id와 매칭되는 Rule를 하나 더 추가한다. 

![0309-15](https://s23.postimg.org/delo04h0r/0309_15.png)

이 설정을 해주면 `서버주소/admin` 페이지에 접근이 가능하다. 이미 데이터베이스를 삭제했으므로 새로운 유저를 생성한다. 

로컬에서 슈퍼유저를 생성한다.(서버에서도 할 수 있음)
```
MODE='DEBUG' STORAGE='S3' DB='RDS' ./manage.py createsuperuser
```

