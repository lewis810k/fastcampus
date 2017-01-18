# 컴퓨터 구조

programmer : 컴퓨터 프로그램을 만드는 사람   
programming : 컴퓨터 프로그램을 만드는 일   
computer program : 컴퓨터에 의해 실행될 수 있는 일련의 기능 모음, 목적을 이루기 위한 명령어의 모음    
computer : **계산**능력을 가진 모든 장치, 데이터와 명령어를 입력 받아 저장하고 이를 **처리**하고 결과를 출력하는 장치

-

### 컴퓨터의 역사
#### 제 1세대 컴퓨터(1941 ~ 1958) : 2차대전 때 시작된 컴퓨터의 초기 모델   
- 소자 : 진공관 (소자란? 장치, 전자 회로 따위의 구성요소가 되는 낱낱의 부품)
- 용도 : 군사, 일기예보
- 기계어 사용
- 18,000개의 진공관, 1,500개의 계전기, 무게 30t, 150kw 전력소비
- 잦은 고장, 고열에 따른 냉각장치 필요, 부피가 매우 큼
- ABC, ENIAC, EDSAC, EDVAC, UNIVAC   

![first_gen_computer](https://sites.google.com/site/fun21stcomputer/_/rsrc/1355060064429/howcomhome/history/785px-Eniac.jpg?height=305&width=400)

-

#### 제 2세대 컴퓨터(1959 ~ 1965) : 냉전시대 초기
- 소자 : 트랜지스터
- 용도 : 사무, 과학기술
- FORTRAN, COBOL 컴파일 언어 사용
- 진공관의 1/200 크기, 저전력, 저가, 낮은 고장률
- 저발열에 따른 냉각기 필요성 감소

![second_gen_computer](http://theevolutionofcomputersovertime.weebly.com/uploads/4/2/2/0/42204035/2146864_orig.png)

-

#### 제 3세대 컴퓨터(1965 ~ 1970) : 컴퓨터 소형화
- 소자 : 집적회로(IC)
- 다리 하나가 전선 하나를 대체함, 소형, 저가, 고속
- 관리, 처리, 사용자 프로그램의 S/W 체계 확립
- 시분할 처리 시스템, 다중처리기법 개발

![third_gen_computer](http://3.bp.blogspot.com/-mBJa4CDd3L0/VLiGUNP6wzI/AAAAAAAAADM/ZW7XLu3v5xk/s1600/tg.jpg)  

-

#### 제 4세대 컴퓨터(1971 ~ 1983)
- 소자 : 고밀도 집적회로
- 마이크로 프로세서 탑재 : Intel CPU
- 컴퓨터 소형화 및 가정용 PC 보급

![fourth_gen_computer](http://4.bp.blogspot.com/-QK9Id-OKRl8/Togf_0RYd_I/AAAAAAAAAAw/HCX-OJ0eG4g/s1600/4th+gene.jpg)

-

#### 제 5세대 컴퓨터(1984 ~ )
- 소자 : 초고밀도 집적회로
- 컴퓨터의 지능화, 자동화, 병렬처리에 초점이 맞춰져 있음
- 고도의 시스템 분야에 활용하고 있으며 계속 개발 진행 중
	
![fifth_gen_computer](http://blog.mirchimart.com/wp-content/uploads/2012/06/apple-imac-all-in-one-desktop-pc.jpg)   

-

### 컴퓨터의 구성
- 컴퓨터는 크게 하드웨어(H/W)와 소프트웨어(S/W)로 나누어진다.

#### 하드웨어   

##### 1. 입력장치
- 사람이 컴퓨터에게 입력하는 정보를 받아들이는 장치
- 키보드, 마우스, 스캐너 등
 
![input_device](http://passnownow.com/wp-content/uploads/2015/10/Input-Devices.jpg)   

-

##### 2. 출력장치
- 컴퓨터의 정보 및 처리 결과를 외부 장치로 내보내는 장치
- 스피커, 모니터, 프린터 등
 
![output_device](http://studentcms.ise.canberra.edu.au/itw/kellkings/wp-content/uploads/sites/52/2014/04/Output-Devices.jpg)

-

##### 3. 처리장치(제어, 연산)
- 컴퓨터가 계산 및 처리를 위해 사용하는 장치
- CPU(Central Processing Unit)
	
![CPU](https://www.bhphotovideo.com/images/images1000x1000/intel_bx80646i54690k_core_i5_4690k_processor_1060511.jpg)

-

##### 4. 기억장치
 
주기억장치 : 컴퓨터 내부에 존재하는 내부 기억장치   
- 용량은 작지만 처리속도는 보조기억장치보다 빠르다.
- CPU에 의해 참조되고, 처리되어야 할 데이터나 프로그램이 기억된다.   

>**ROM**   
>: 주로 시스템이 필요한 내용을 제조단계에서 기억시킨 후 사용자는 기억된 내용을 읽기만 하는 장치   
>: 전원 공급이 중단되어도 기억된 내용을 유지하는 비휘발성         

>**RAM**   
>: 읽기/쓰기가 모두 가능한 기억장치   
>: 전원이 차단되면 기억된 데이터가 손실되는 휘발성  

보조기억장치 : 주기억장치의 용량 부족을 해결하기 위해 컴퓨터 외부에 마련된 대용량 기억장치
- 주기억장치보다 속도는 느리지만 용량이 크다. 
- 비용이 저렴하다. 
- 데이터의 장기보관이 가능하다.
- Magnetic disk, magnetic tape, magnetic drum, CD-ROM등이 있다.   

기타장치로는 Cache Memory, Virtual Memory, Flash Memory등이 있다.

-

#### `메모리 계층`

![memory](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter1/1_4_StorageDeviceHierarchy.jpg)   

위로 올라갈 수록 
- CPU에 가까워진다.
- 가격 비싸진다.
- 속도가 증가한다.
- 용량이 작아진다.   

>집(보조기억장치)에서 자주 쓸만한 물건들을 옆 강의실 사물함(주기억장치)에 두었을 때 강의실 책상(캐시메모리) 당장 쓸 물건들을 가져다 놓고 사용한다. 필요한 물건이 있을 때마다 집을 들리는 것보다 미리 사물함에 가져다 놓았을 경우 사용할 수 있는 공간은 작지만 접근속도는 매우 빠르다.

-

#### 소프트웨어  

**시스템 소프트웨어**   
- 응용 소프트웨어를 실행하기 위한 플랫폼을 제공한다.
- 컴퓨터 하드웨어를 동작, 접근할 수 있도록 해준다.
- 운영체제, 로더, 장치드라이버, 컴파일러, 링커, 유틸리티, 어셈블러   

**응용 소프트웨어**
- 넓은 의미에서는 운영 체제에서 실행되는 모든 소프트웨어를 뜻한다. 
- 응용 소프트웨어는 시스템 소프트웨어에 종속적인 한계가 있다.
- 애플리케이션, 워드프로세스, 게임 등 주로 직접적으로 제작하는 프로그램

-

### 컴퓨터 구조
#### 폰 노이만 구조 ####
- 프로그램과 데이터를 하나의 메모리에 저장한다.
- 메모리 속박 문제 및 버스 병목 문제를 가진다. 
- 명령어를 읽는 동안 데이터를 읽을 수 없다.

#### 하버드 구조 ####
- 프로그램과 데이터를 물리적으로 구분하여 다른 메모리에 저장한다.
- 명령어를 읽는 동안 데이터를 읽을 수 있어 속도가 빠르다. 
- 구성에 비용이 많이 들며 설계가 복잡하다.

![arch](http://www.edgefxkits.com/blog/wp-content/uploads/Differences-between-Von-Neuman-Architecture-and-Harvard-Architecture.jpg)

#### 폰 노이만 + 하버드 ####
- 최신 컴퓨터 구조는 폰 노이만 구조와 하버드 구조를 결합한 모습을 띈다.
- 하버드 구조의 비용과 복잡성 문제를 해결하기 위함.
- CPU의 캐시 메모리 향상에 관여   
: CPU내부 - 하버드 / 외부 - 폰 노이만    
: 하버드 구조가 CPU내부에서 캐시역할을 수행한다.    
: 폰 노이만 구조에서 명령어를 받아오면 CPU안에 있는 하버드 구조가 메모리에 다음 명령어를 계속해서 추가해놓는다.

