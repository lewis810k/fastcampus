# 알고리즘
- 문제해결을 위한 절차/방법   
- 어떠한 문제를 해결하기 위한 여러 동작들의 모음  
- 대표적 알고리즘 - 정렬, 탐색, 재귀 등

> 여행가방을 꾸린다. 자동차를 고친다. 점심을 먹는다 등 어떤 문제를 해결해 나가는 과정

>알고리즘과 자료구조는 환상의 궁합  
 
정렬 알고리즘만해도 다양한 알고리즘이 존재한다.  

**그 이유는?**  
: 데이터가 어떤 분포 형태를 취하는 지에 따라, 목적에 따라, 시스템에 따라 적합한 방법이 다양하다. 

**컴퓨터의 자원은 한정적이기 때문에 얼마나 효율적으로 사용하느냐가 성능의 큰 관건이 된다.**  

### [정렬]

#### 선택정렬(Selection Sort)
![selection](http://img.wonderhowto.com/img/65/56/63594174248792/0/sorting-part-5-0-selection-sort.w1456.jpg)

각 Step에서는 가장 작은 수를 `선택`하여 그 수가 위치해야할 곳과 자리를 바꿔준다.  

Step 1에서는 가장 작은 수인 1이 `선택`되고 가장 왼쪽(3)과 자리를 바꿔준다.  
Step 2에서는 1을 제외하고 가장 작은 수(2)를 `선택`하고 1 다음 숫자(9)와 바꿔준다.  
Step 2가 지나면 1과 2는 오름차순으로 정렬이 된 상태이다.  
나머지도 같은 방법으로 진행한다.   

---
 
#### 버블정렬(Bubble Sort)
![bubble](http://www.programiz.com/sites/tutorial2program/files/Bubble-sort-algorithm-programming.jpg)
  
Step 1을 거치면 가장 큰 수(45)가 가장 우측에 위치한다.  
Step 2를 거치면 두번째로 큰 수(11)가 우측에서 두번째 자리에 위치한다.  
전체의 데이터 수가 N개 라면 N-1개의 Step을 통해 정렬이 완료된다.   

화살표의 형태가 버블형태로 이루어진다고 해서 버블정렬이라고 한다.

---

#### 삽입정렬(Insetion Sort)
![insertion](http://freefeast.info/wp-content/uploads//2013/01/Insertion-Sort-Model11.jpg) 

삽입정렬은 각 Step에서 우측의 데이터를 하나씩 선정하여 순서에 맞게 좌측 데이터에 `삽입`한다.  
Step 2만 봤을 때, 2와 5는 이미 정렬된 상태이고, 4를 선정하여 맞는 위치에 `삽입`하는 프로세스로 진행된다.  

---

#### 퀵정렬(Quick Sort)
![quick](http://www.thecshandbook.com/public_html/img/uploads/quicksort.png)

퀵정렬에서는 `pivot`개념이 등장한다.  
**기준이 될 숫자**를 `pivot`으로 정한다.  
5가 `pivot`으로 정해지면 좌측에는 5보다 작은 수, 우측에는 5보다 큰 수를 보낸다.  

`좌측열` | `pivot` | `우측열`  
위의 상태로 데이터가 정리된다. 

좌측열과 우측열에 대해 각각 `pivot`을 별도로 정하여 위의 작업을 다시 진행한다.  
`pivot`이 더 이상 나오지 않는 상황(정렬이 다 된 상황)에서 데이터를 합친다.  

>퀵정렬은 평균적인 퍼포먼스가 가장 빠르다.  
퀵정렬에서는 pivot을 어떻게 선정하느냐에 따라 퍼포먼스가 달라진다.

---

#### 병합정렬(Merge Sort)
![merge](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e6/Merge_sort_algorithm_diagram.svg/300px-Merge_sort_algorithm_diagram.svg.png)

병합정렬은 매 step마다 데이터를 **두 파트로 나눈다.**  
<pre>
	38 | 27 | 43 | 3 | 9 | 82 | 10
</pre>
위 상황은 데이터가 두 파트로 나눠져서 더 이상 나눠질 수 없는 상황이다.  
이 때 두 파트씩 `병합`하면서 동시에 정렬을 한다.  

---

#### 시간복잡도(Time Complexity)
- 알고리즘이 실행되는데 소요되는 시간분석   
- 점근 표기법 사용

정렬 과정에서 같은 크기의 배열을 하나 더 만들어서 가장 작은 값 순서대로 새로운 배열에 넣는다고 가정하면 속도의 개선은 있지만 공간의 낭비가 발생한다.  

>시간복잡도와 공간 복잡도는 반비례 한다. 

시간복잡도는 개인이 더 찾아보는게 나을 것 같다.   

1. [시간복잡도 vs 공간복잡도](http://ledgku.tistory.com/33)  
2. [시간복잡도](http://gwpark.tistory.com/1731)

---
