# Django Deploy(5)

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

### pgAdmin으로 RDS 접속

로컬에서 pgAdmin 가상환경이 세팅된 경로로 가서 pgAdmin을 연다. 
```
python ~/.pyenv/versions/3.5.2/envs/pgadmin_env/lib/python3.5/site-packages/pgadmin4/pgAdmin4.py
```
어느 경로에서든 명령어가 동작하게 하려면 zsh alias를 설정한다. (추가예정)

새로운 서버를 생성한다. 

![0309-12](https://s17.postimg.org/a3uboeo6n/0309_12.png)

-

![0309-11](https://s17.postimg.org/olriwefhr/0309_11.png)

connection 탭에서 host name은 RDS 인스턴스의 end point 주소를 사용한다. 이 때 마지막 포트번호는 제외하고 `.com`으로 끝나도록 한다. 

나머지 정보도 RDS 생성시 입력한 정보와 동일하게 작성하고 SAVE를 누른다. 


