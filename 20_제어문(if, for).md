# 제어문

**조건문**과 **반복문**에 대해 살펴보자.   

### 조건문
프로그램을 작성하면서 조건에 따라 실행해야 하는 문장이 달라지는 경우가 있다.   

###if

```python
if x > 0 :
    print("x는 0보다 크다")
```

`x > 0`이 **조건**에 해당되며 `만약 x가 0보다 크면`으로 해석할 수 있다.  
x가 5라면 x > 0이라는 조건에 대해 참(true)이기 때문에 print가 실행된다.  

조건절이 끝나면 반드시 **콜론(:)**을 사용한다.  

if와 print사이에는 들여쓰기가 되어있다.   
if가 true이면 if보다 더 들여쓰기 된 코드들이 실행된다.  
```python
if x > 0 :
    print("x는 0보다 크다")
    x = 0
    print("현재 x는 0입니다.")
print("프로그램을 종료합니다.")
```
if 조건이 참이면 들여쓰기가 된 아래의 3줄 모두 실행된 다음 "프로그램을 종료합니다" 라는 메시지가 출력된다. 만약 if 조건이 만족되지 않는다면 "프로그램을 종료합니다"라는 메시지 한 줄만 출력된다.  

### if ~else

조건문의 결과에 따라 다른 문장들이 실행된다.  


```python
if x % 2 == 0 :
    print('짝수입니다.')
else :
    print('홀수입니다.')
```
x가 -1이라면 if조건에 대해 false이기 떄문에 else가 실행된다.  
else는 if조건에 해당하지 않는 모든 경우이기 때문에 조건을 따로 적지 않고 콜론(:)만 적는다.  

### if ~elif
조건이 여러개로 나눠질 경우 추가적인 조건에 대해 elif로 처리한다.  

```python
score = 75

if score >= 90 :
    print('당신의 성적은 A입니다.')
elif score >=80:
    print('당신의 성적은 B입니다.')
elif score >= 70:
    print('당신의 성적은 C입니다.')
elif score >= 60:
    print('당신의 성적은 D입니다.')
else:
    print('당신의 성적은 F입니다.')
```
score가 75이기 때문에 `'당신의 성적은 C입니다.'`가 출력된다. 

가장 상위에 있는 조건들이 먼저 체크된다. 그리고 elif와 else로 구성되어 있다면 **조건이 참이 되는 순간 더 이상 비교를 하지 않는다.**  

score가 90이상인지 체크해 본 다음 아닐 경우 80이상인지 체크한다. 아닐 경우 70이상인지 확인하고 조건이 참이 되는 순간 print('당신의 성적은 C입니다.')가 출력된다. 그리고 60이상인지, else인지는 비교해보지 않는다.  

```python
fruits = ['apple', 'banana', 'grape', 'orange', 'strawberry']
fruit_input = input('좋아하는 과일을 입력하세요 : ')
if fruit_input in fruits :
    print('과일이 목록에 있습니다.')
elif fruit_input == "없음" :
    print('과일을 먹어보세요 분명히 좋아하게 될 겁니다.')
else :
    print('과일이 목록에 없습니다.')
```
fruits 리스트에 5개의 과일 이름이 들어있다.   
input() 함수를 통해 사용자로부터 좋아하는 과일 이름을 입력받는다.   
- 입력받은 과일 이름이 fruits 리스트에 있을 경우  
- 없음 이라고 입력한 경우  
- 입력받은 과일 이름이 fruits 리스트에 없는 경우로 나눠진다.  

### if 중첩

```python
if x == y :
    print ('{}와 {}는 같습니다.'format(x, y))
else :
    if x < y :
        print('{}가 {}보다 작습니다.'.format(x, y))
    else :
        print('{}가 {}보다 큽니다.'.format(x, y))
```
먼저 x와 y가 같은지 아닌지를 판별한다.  
`같다면` else는 실해되지 않는다.   
`다르다면` 또 다른 if else에 의해 x와 y중 어떤 값이 더 큰지 비교하게 된다. 

### 조건표현식
```python
참일경우 if 조건식 else 거짓일 경우
```
is_holiday에 True또는 False값을 할당한 후, if문과 조건표현식을 사용해서 각각 'Good'과 'Bad'를 출력하는 코드를 짜본다.
```python
>>> is_holiday = True
>>> print('good') if is_holiday == True else print('bad')
good
 
>>> is_holiday = False
>>> print('good') if is_holiday == True else print('bad')
bad
```

### 중첩조건표현식
```python
# 조건이 2개일 경우
조건1이 참일경우 if 조건1 else 조건1은 거짓이나 조건2가 참일경우 if 조건2 else 조건1,2가 모두 거짓일 경우
```
vacation에 1에서 10중 아무 값이나 할당 후, if, elif, else문과 중첩 조건표현식을 사용해서 각각 vacation이 7이상이면 'Good', 5이상이면 'Normal', 그 이하면 'Bad'를 출력하는 코드를 짜본다.
```python
>>> vacation = 5
>>> print('Good') if vacation >= 7 else print('Normal') if vacation >= 5 else print('bad')
normal
```

---

# 반복문

종종 반복되는 일들을 처리할 때 
주로 시퀀스 타입 데이터를 순회할 때 사용한다. 

### for 반복문
반복가능한 문자열, 리스트, 딕셔너리, 셋과 함께 사용한다.   
내장함수인 in과 range()를 주로 활용한다.  

딕셔너리에서 키나 값을 순회할 때는, iterable한 객체의 위치에 dict.keys()나 dict.values()를 사용한다.  
키, 값을 모두 순회할 때에는 dict.items()를 사용한다.
```python
for 항목 in 순회가능(iterable)객체:
   <항목을 사용한 코드>
```

```python
range(start, end, step)

>>> list(range(5))
[0, 1, 2, 3, 4]

>>> list(range(0, 5))
[0, 1, 2, 3, 4]
```
range함수를 list형태로 변환하여 출력해보면   
ragne함수는 0부터 시작하여 1씩 증가하는 숫자 5개를 보여준다.   
range(n) 을 할 경우 0 ~ n-1까지 순회하는 것이다.   

range(0, 5)라고 하면 0 ~ 4까지 순회하는 것이고  
range(5)처럼 범위를 지정하지 않으면 **자동으로 시작지점이 0이 된다**.  

```python
>>> list(range(1, 11, 2))
[1, 3, 5, 7, 9]
```
range함수에서 3번째 인자값을 넣어주면 step을 의미하게 된다.  
아무 값도 지정해주지 않을 경우 디폴트가 1이 되고,  
1~ 10까지의 범위내에서 2씩 증가하는 리스트를 순회한다.  

```python
>>> list(range(10, 0, -1)
[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```
step에 음수값을 넣게 되면 증가가 아니라 감소한다.  
start지점이 10이고 0이 되기 전까지 줄어든다.   

-

### for 사용 방식

**for문은 주로 2가지 형태로 나타난다.**  

리스트의 각 요소를 순회한다고 했을 때,

- element에 직접 접근하는 방법  
- range를 통해 인덱스로 접근하는 방법  


```python
>>> fruits = ['apple', 'banana', 'grape', 'orange', 'strawberry']

>>> for item in fruits:       #element에 직접접근
>>>     print(item)
'apple'
'banana'
'grape'
'orange'
'strawberry'

>>> for i in range(len(fruits)):    #index를 이용하여 접근
>>>     print(fruits[i])
'apple'
'banana'
'grape'
'orange'
'strawberry'
```

첫번째 for문은 **fruits안에 있는 각 element를 item이라는 변수로 받아서 사용하겠다.**   
두번째 for문은 **fruits 길이 만큼 반복하는 for문에서 i를 인덱스로 활용하여 사용하겠다.**  
라는 의미가 된다. 

>len()은 데이터의 길이 값을 정수로 반환해주는 함수이다.   

-

### 중첩 for

위에서 반복문은,  
종종 반복되는 일들을 처리할 때 사용한다고 했다.  
반복되는 일의 묶음 자체가 또 반복된다면 어떻게 될까  

구구단을 출력할 때 2단만 출력한다면 다음과 같이 표현할 수 있다. 
```python
for i in range(1, 10):
   print('2 x {} = {}'.format(i, i*2))
```
2단만 출력하기 때문에 2는 고정이고 2에 곱해지는 수가 1에서 9까지 1씩 증가하는 수이다.   

여기서 2단이 아니라 2단에서 9단 모두 출력하게 된다면  
위에서 1에서 9까지 증가하면서 곱해줬던 반복적인 작업이  
2단, 3단, 4단, ... ,8단, 9단에 걸쳐서 반복되어야 한다.  

```python
for i in range(2, 10):                   #2단~9단을 순회
    print('\n{}단 입니다.'.format( i ))
    for j in range(1, 10):                  #각 단에서 1~9곱을 순회
        print('{} x {} = {}'.format( i, j, i * j ))
```

{}단 입니다. 라는 문장이 출력되는 효과는 다음 코드를 보면 조금 더 명확할 것이다.   
다음 코드에서 a의 역할을 한다.  

```python
for i in range(3) :
    print ('a')
    for j in range(3) :
        print ('    b')
        
a
     b
     b
     b
a
     b
     b
     b
a
     b
     b
     b
        
```

-

### break와 continue

결론부터 말하면.  
break는 가장 가까운 반복문을 빠져나오고   
continue는 아래의 코드를 실행하지 않고 가장 가까운 반복문으로 돌아간다.  

```python
for i in range(10):
    if i == 5:
        break             #반복문 종료
    print( i )
0
1
2
3
4    
```
break가 없다면 0~9까지 출력되어야 하지만 i 가 5일 때 break가 작동되기 때문에 더 이상 반복하지 않고 반복문을 빠져나온다. 

**continue의 경우는 조금 다르다. **   
```python
for i in range(10):
    if i == 5:
       continue          #다른 코드 진행하지 않고 반복문으로 돌아감
    print( i )
0
1
2
3
4    
6
7
8
9
```
i가 5일 때 continue가 동작한다. continue는 나머지 코드를 실행하지 않고 바로 반복문으로 돌아가서 다음 반복을 진행하기 때문에 i가 5일 때 print가 동작하지 않는다.  

### 중첩 for에서의 break  
```python
for i in range(2, 10):                   #2단~9단을 순회
    print('\n{}단 입니다.'.format( i ))
    for j in range(1, 10):                  #각 단에서 1~9곱을 순회
        print('{} x {} = {}'.format( i, j, i * j ))
```
위에 있던 구구단 코드를 활용해보자.  

```python
for i in range(2, 10):                   #2단~9단을 순회
    if i == 5:                                #i가 5일 때 반복 종료
        break
    print('\n{}단 입니다.'.format( i ))
    for j in range(1, 10):                  #각 단에서 1~9곱을 순회
        print('{} x {} = {}'.format( i, j, i * j ))
```
위의 경우에는 2단 3단 4단이 정상적으로 출력되고 **5단부터는 반복이 종료되어 더이상 출력되지 않는다.**

```python
for i in range(2, 10):                   #2단~9단을 순회
    print('\n{}단 입니다.'.format( i ))
    for j in range(1, 10):                  #각 단에서 1~9곱을 순회
        if i == 5:                            #i가 5일 때 반복 종료
            break
        print('{} x {} = {}'.format( i, j, i * j ))
```
이번에는 break가 안에 있는 for문에 걸려있다.  
이 경우에는 2단 3단 4단이 정상적으로 출력되고 5단일 때 "5단입니다"라는 메시지는 띄우지만 두번째 for문에서 break에 걸리게 된다. break는 가장 가까운 반복문에만 해당되기 때문에 두번째 for문을 종료하고 다시 6단부터 정상적으로 실행된다.  
**즉, 5단을 제외하고는 모두 정상적으로 출력되는 코드이다.**

### while 반복문

while 반복문은 if 조건문과 동작이 비슷하다.  
if조건문의 반복이라고 표현해도 될 정도이다.  

```python
x = 10
if x > 5 :
    print('x는 5보다 큽니다.')

x는 5보다 큽니다.
```
일반 if문의 경우 조건 체크가 한 번만 이루어진다.   

```python
x = 10
while x > 5 :
    print('x는 5보다 큽니다.')
    
x는 5보다 큽니다.
x는 5보다 큽니다.
x는 5보다 큽니다.
x는 5보다 큽니다.
.
.
.
.

```
while 반복문은 while안의 내용을 실행하고 다시 조건을 검사하여 참이 되면 또 실행한다.  

### *while문은 반드시 종료조건을 명시해야 한다. 
```python
x = 10
while x > 5 :
    print('x는 5보다 큽니다.')
    x = x - 1
    
x는 5보다 큽니다.    # x == 10
x는 5보다 큽니다.    # x == 9
x는 5보다 큽니다.    # x == 8
x는 5보다 큽니다.    # x == 7
x는 5보다 큽니다.    # x == 6
```
마지막에 x가 6일 때 while문을 들어가서 'x는 5보다 큽니다'라는 메시지를 출력한 다음 x 값은 5가 된다.   
while문의 조건이 5 > 5가 되기 때문에 false이므로 더 이상 반복을 진행하지 않는다.  
> 종료조건을 명시하지 않을 경우 무한반복이 발생할 수 있다. 

---

### 컴프리헨션 (Comprehension)

리스트 컴프리헨션  
```
[표현식 for 항목 in iterable객체]
```

#### [1, 2, 3, 4, 5]를 만드는 방법

range와 for문을 사용할 경우
```python
>>> numbers = []
>>> for item in range(1, 6):
...   numbers.append(item)
... 
>>> numbers
[1, 2, 3, 4, 5]
```
리스트 컴프리헨션을 사용할 경우
```python
>>> [item for item in range(1, 6)]
[1, 2, 3, 4, 5]
```

리스트 형식으로 출력되기 때문에   
item for item in range(1, 6)
위의 구문 전체에 [  ] 로 감싸진다.  

만약 각 item에 2배의 값을 할당하고 싶다면?
```python
>>> [item * 2 for item in range(1, 6)]
[2, 4, 6, 8, 10]
```

만약 1~5중 짝수만 해당하는 리스트를 만들고 싶다면?  
for 뒤에 조건문을 명시한다. 
```python
>>> [item for item in range(1, 6) if item % 2 == 0]
[2, 4]
```

리스트 컴프리헨션의 중첩
```python
for color in colors:
  for fruit in fruits:
```
```python
[(color, fruit) for color in colors for fruit in fruits]
```
리스트 컴프리헨션에서 첫번째 for문이 상위에 해당하고 두번째 for문이 하위에 해당한다.  

실습풀이 파일을 보면 조금 더 공부가 될 것 같다.
