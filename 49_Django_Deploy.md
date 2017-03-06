# Ubuntu Linux Deploy

> AWS EC2 + Ubuntu16.04 + Nginx + uWSGI + Django

### EC2 Instance생성

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

```













```
