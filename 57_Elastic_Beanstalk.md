# Elastic Beanstalk

### EB CLI 설치 
(Elastic Beanstalk Command Line Interface)

deploy_eb_docker 프로젝트의 가상환경으로 들어와서 awsebcli를 설치한다.
```
pip  install awsebcli
```
EB CLI는 로컬 저장소에서 환경을 생성, 업데이트, 모니터링 작업을 단순화한다.

-

### Configure EB

AWS IAM에서 유저 권한을 추가한다. 
```
AWSElasticBeanstalkFullAccess
```

설정
```
eb init

region: seoul
Application Name : FC Deploy Elasticbeanstalk with Docker
자동으로 docker 사용을 감지한다. Y입력
platform version : 1.12.6
keypair : 리스트 중에서 사용하고 있는 keypair를 선택한다. 
```

설정을 마치면 프로젝트 내부에 .elasticbeanstalk라는 디렉토리가 생성된다. 

![0313-1](https://s27.postimg.org/70qud95ir/0313_1.png)

그리고 config.yml에는 설정했던 내용들이 들어있다.

-

### Dockerfile.aws.json

프로젝트의 main 디렉토리에 Dockerfile.aws.json을 생성한다. Dockerfile에 의해서 생성된  컨테이너를 AWS에서 어떻게 사용할지에 대한 설정을 담당한다. 

```
{
  "AWSEBDockerrunVersion": 2,
  "volumes": [
    {
      "name": "fc-deploy-eb",			# 자유롭게
      "host": {
        "sourcePath": "/var/app/current/django_app"
      }
    }
  ],
  "containerDefinitions": [
    {
      "name": "fc-deploy-eb",
      "essential": true,
      "memory": 512,
      "portMappings": [
        {
          "hostPort": 80,
          "containerPort": 4040
        }
      ]
    }
  ]
}
```

-

### .ebignore

현재 .conf-secret이 .gitignore에서 관리되고 있기 때문에 commit할 때 .conf-secret의 내용이 추가되지 않는다. 

```
!.conf-secret/
```
.ebignore은 elasticbeanstalk에서 .gitignore의 내용 중 .conf-secret은 무시하게 된다. eb deploy할 때 .conf-secret은 항상 포함된다.  

.ebignore을 commit한다. 

-

### EB CLI로 관리하기

[EB CLI 기본명령어](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-getting-started.html)

elasticbeanstalk 환경을 만든다.
```
eb create
```
적절히 이름을 짓고 나머지 문항에 대해서도 엔터를 누른다. 

자동으로 환경을 세팅하는데 제법 오래 걸릴 수도 있다. 

배포를 진행한다.
```
eb deploy
```
deploy할 때 ubuntu:16.04부터 시작해서 모든 패키지를 설치, uwsgi, nginx 설정하는 과정을 다 거치기 때문에 작업이 오래 걸린다.

eb deploy 하고나서 eb ssh명령어로 eb에 접근해보면 sourcePath로 지정해두었던 경로에 파일이 업로드된 것을 확인할 수 있다. 

