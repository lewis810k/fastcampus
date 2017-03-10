# Filezilla 사용법

- 

### Filezilla?

> 파일질라(FileZilla)는 오픈 소스 크로스 플랫폼의 자유 소프트웨어인 FTP 소프트웨어이며 파일질라 클라이언트와 파일질라 서버로 이루어져 있다. 이진 파일은 윈도, Mac OS X, 리눅스용으로 이용할 수 있다. FTP, SFTP, FTPS (SSL/TLS 위의 FTP)를 지원한다  
> [FileZilla 위키백과](https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%BC%EC%A7%88%EB%9D%BC)

한마디로 서버의 파일을 커맨드창이 아니라 다음과 같이 볼 수 있도록 해주며 로컬-서버 간 업로드 다운로드가 자유롭게 할 수 있도록 해주는 프로그램이다.

![0310-1](https://s2.postimg.org/v25yrjpex/0310_1.png)

-

### 설치

```
$ sudo add-apt-repository ppa:n-muench/programs-ppa
$ sudo apt-get update
$ sudo apt-get install filezilla
```

#### filezilla 열기
```
filezilla
```
커맨드창에서 filezilla를 입력하거나 applications에서 filezilla를 직접 실행한다.

-

### 호스트추가

앞으로 접속하게 될 서버 호스트를 추가한다. 우리의 경우 deploy_ec2라는 이름으로 사용하고 있는 AWS EC2 서버이다. 

![0310-2](https://s28.postimg.org/bv0f3zkpp/0310_2.png)

filezilla에서 좌측상단에 있는. Host 글자 바로 위에 있는 버튼을 클릭하면 Site Manager 창이 열린다.

![0310-3](https://s28.postimg.org/x5xz892u5/0310_3.png)

좌측에서 New Site를 추가하고 이름을 지정한다. 자신이 알아보기 편하게 지정하면 된다.   
우측은 다음과 같이 세팅한다.  
```
Host: 			자신의 EC2 주소
Protocol :		SFTP
Logon Type :	Normal	
User :			ubuntu
```
EC2 인스턴스를 ubuntu 서버로 생성했기 때문에 ubuntu를 입력한다. password는 따로 입력하지 않는다.

이렇게 생성하면 키에 대한 팝업창이 열린다. Always trust... 을 클릭하고 OK 누른다.

이 때 서버에 연결을 시도하지만 연결이 되지 않을 것이다. 키가 없기 때문이다. 

-

### 키설정

AWS EC2를 생성하면서 .pem 키를 다운받았을 것이다. 이 파일을 filezilla에 등록하는 작업이다. 

![0310-4](https://s30.postimg.org/int8fbdc1/0310_4.png)
```
상단바에서 Edit -> Preferences를 누른다. 
```
(ubuntu에서는 프로그램이 최대화 상태이어야 상단바가 보인다?)

```
Connection -> SFTP -> Add key file...
```
자신의 .pem 파일을 선택한다. 우리는 이미 .ssh 폴더에 키 파일을 넣어두었는데 키 선택창에서는 숨김폴더가 보이지 않는다. 

```
cd ~/.ssh
cp 자신의키.pem 경로  (예시: cp lewis.pem ../Downloads)
```
이렇게 복사한 다음 키를 등록하고 OK 버튼을 누른다. 

-

### 연결하기

![0310-3](https://s28.postimg.org/x5xz892u5/0310_3.png)  

site manager로 가서 connect 버튼을 누른다. 

![0310-5](https://s7.postimg.org/sbwhdi1az/0310_5.png)

연결된 화면이다. 좌측이 로컬측이고 우측이 서버측이다. 파일을 자유롭게 업로드 다운로드 할 수 있다. 


