# 네트워크

### 네트워크란?
**Net + Work의 합성어로 컴퓨터들이 통신 기술을 이용하여 그물망처럼 연결된 통신 이용 형태를 의미한다.**

---

### TYPE

규모에 따라 종류를 구분한다. 

![type](http://www.assignmenthelp.net/assignment_help/images/computer-networks-and-types.png)

- LAN(Local Area Network) : 근거리 통신망   
- MAN(Metropolitan Area Network) : 도시권 통신망   
- WAN(Wide Area Network) : 광역 통신망   

---

### Internet?   

![internet](https://i.stack.imgur.com/ZbTUk.png)

- Inter Network   
- **네트워크의 네트워크**   
- 컴퓨터로 연결하여 TCP/IP 프로토콜을 이용해 정보를 주고 받는 컴퓨터 네트워크   
- **TCP/IP** : 인터넷 이용시 지켜야 할 규칙  


### WWW
- World Wide Web
- 문서(웹페이지)들이 있는 정보의 저장소 
- 분산과 연결
- 군사적 목적에 의해 만들어짐

>월드 와이드 웹은 인터넷에 연결된 컴퓨터들을 통해 사람들이 정보를 공유할 수 있는 전 세계적인 정보 공간을 말한다.  

### URL(Uniform Resource Locator)
- 인터넷에 접속할 때, 네트워크 상에서 해당 자원이 어디 있는지를 알려주는 문자열
- 접속하려면 해당 URL에 맞는 프로토콜을 알아야 하고, 그와 동일한 프로토콜로 접속해야 한다.
- [Protocol]://[Host]/[port]/[path]

### Protocol
- 통신규약
- 장비 사이에서 메시지를 주고 받는 양식과 규칙의 체계 즉, 통신(네트워킹)할 때 정해진 메시지 규칙  
- http, https, ftp, sftp, telnet, ssh, ssl, smtp... 

>**HTTPS(HTTP over SSL)**   
>HTTP의 보안이 강화된 버전이다. HTTPS는 소켓 통신에서 일반 텍스트를 이용하는 대신에, SSL이나 TLS 프로토콜을 통해 세션 데이터를 암호화한다. 따라서 **데이터의 적절한 보호를 보장한다.**

### HTTP METHOD
Hyper Text Transfer Protocol   
HTTP는 URL을 통해 특정 웹페이지의 정보를 주고 받는데 여러 방식이 사용된다. 

- **GET** : URL이 가진 정보를 검색하기 위해 서버 측에 요청, URL을 통해 데이터를 전송하기도 한다.
- **POST** : 부가적인 데이터를 보낼 때 사용
- **PUT**
- DELETE
- HEAD
- TRACE
- OPTIONS
- CONNECT

### #GET과 POST의 차이?  
- **데이터 명시성**  
GET은 요청사항을 URL에 명시적으로 추가해서 요청한다. 개인정보 등 민감한 데이터의 경우 POST 방식을 사용하여 명시적으로 추가하지 않아도 전송되도록 한다.  
- **데이터 한정**   
URL의 길에 제한으로 인해 GET으로 보낼 수 있는 데이터가 한정적이다. POST는 한정적이지 않아서 글쓰기 같은 경우 POST방식으로 처리해야 한다.  
- **데이터 접근 방식**  
GET방식은 캐싱된 데이터를 받아올 수 있기 때문에 정보에 대한 접근이 빠르다. 하지만 캐싱된 데이터가 서버의 데이터로 최신화되지 않았을 경우 잘못된 정보를 불러올 수 있다. 새로고침은 캐싱된 데이터가 아닌 서버에 있는 데이터를 받아오게 한다.
- **목적**  
GET은 데이터를 가져와서 보여주는 용도가 강하고, POST는 서버의 값이나 상태 등을 바꾸기 위해 주로 사용한다. 


**FTP**(File Transfer Protocol)  
: 파일 전송 통신규약    
**TELNET**(TErminaL NETwork)   
: 원격 로그인을 위한 프로토콜   

###SSH(Secure Shell)   
- TELNET의 대용 목적으로 설계  
- 네트워크 상의 다른 컴퓨터에 로그인하거나 원격 시스템에서 명령을 실행하고 다른 시스템으로 파일을 복사할 수 있도록 해주는 프로토콜    
- 강력한 인증 방법 및 안전하지 못한 네트워크에서 **안전하게 통신**을 할 수 있는 기능을 제공한다.   

###SSL(Secure Socket Layer)   
- https = http + ssl   
- **웹서버와 브라우저 사이의 보안**을 위한 프로토콜  

>실제로 HTTPS는 웹 서버와 브라우저 사이에 전송되는 카드 정보만을 암호화 할 뿐이다. 카드 정보는 보통 서버 데이터베이스에 저장되며, 신용카드 처리기에는 전송되지도 않는 경우가 많다. 대부분의 정보 유출은 내부 인력에 의해 일어난다.

###SMTP(Simple Mail Transfer Protocol)
전자메일 **발송** 프로토콜  
서버에 있는 메일을 수신할 때는 **POP, IMAP**을 사용  
  