# PYTHON 환경구축 (for Ubuntu)

## pyenv, virtualenv, iPython 설치 및 설정

### pyenv
**설치 및 설정**
github으로 설치하기

터미널에 아래의 명령어를 입력하면 github을 통해 자동으로 설치된다. 
```
curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
```
설치 후 설정을 위해 .zshrc파일을 vim명령어로 연다.
```
vim ~/.zshrc
```
.zshrc 파일 맨 아래에 다음 내용을 추가한다.

```
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

다음과 같이 추가하고 저장 후 종료한다.

터미널을 `재시작`하거나 `source ~/.zshrc` 명령어로 변경사항을 반영한다. 

![pyenv](https://s30.postimg.org/61aid3oo1/pyenv.png)

### vim 설치
```
sudo apt-get install vim
```

### 기본 shell을 zsh로 변경하기
```
sudo apt-get install zsh
curl -L http://install.ohmyz.sh | sh
chsh -s `which zsh`
```
명령어를 한 줄씩 입력하고 나서 터미널을 재실행하여 반영되도록 한다.

`echo $SHELL` : 현재 shell 확인하기


### Python 설치
Python 설치 전에 먼저 다음 패키지를 설치한다 
```
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils
```
pyenv를 통해 python을 설치한다. 
```
Pyenv install 3.4.3 (python 버전 3.4.3 설치)
```
permission denied가 발생하여 설치가 진행되지 않을 시 `.zshrc`에 추가한 내용 중 경로를 다시 살펴본다.

가상환경을 생성한다.
```
Pyenv virtualenv 3.4.3 fc-python 
(pyenv virtualenv [version] [env-name])
```
작업을 진행할 폴더로 이동한다. (git의 working directory 생성과 비슷)  
```
cd projects
mkdir python
cd python
```
local에 가상환경 지정
```
pyenv local fc-python
```
ipython 설치
`pip install ipython`을 입력한다.
`ipython`으로 python shell을 연다. 
