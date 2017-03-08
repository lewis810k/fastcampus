# Django Deploy(4)

- boto3

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


