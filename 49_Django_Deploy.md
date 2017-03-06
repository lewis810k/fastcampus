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
