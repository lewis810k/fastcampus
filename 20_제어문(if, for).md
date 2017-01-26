# 제어문

**조건문**과 **반복문**에 대해 살펴보자.   

### 조건문
프로그램을 작성하면서 조건에 따라 실행해야 하는 문장이 달라지는 경우가 있다.   

###if

```
if x > 0 :
    print("x는 0보다 크다")
```

`x > 0`이 **조건**에 해당되며 `만약 x가 0보다 크면`으로 해석할 수 있다.  
x가 5라면 x > 0이라는 조건에 대해 참(true)이기 때문에 print가 실행된다.  

조건절이 끝나면 반드시 **콜론(:)**을 사용한다.  

if와 print사이에는 들여쓰기가 되어있다.   
if가 true이면 if보다 더 들여쓰기 된 코드들이 실행된다.  
```
if x > 0 :
    print("x는 0보다 크다")
    x = 0
    print("현재 x는 0입니다.")
print("프로그램을 종료합니다.")
```
if 조건이 참이면 들여쓰기가 된 아래의 3줄 모두 실행된 다음 "프로그램을 종료합니다" 라는 메시지가 출력된다. 만약 if 조건이 만족되지 않는다면 "프로그램을 종료합니다"라는 메시지 한 줄만 출력된다.  

### if ~else

조건문의 결과에 따라 다른 문장들이 실행된다.  


```
if x % 2 == 0 :
    print('짝수입니다.')
else :
    print('홀수입니다.')
```
x가 -1이라면 if조건에 대해 false이기 떄문에 else가 실행된다.  
else는 if조건에 해당하지 않는 모든 경우이기 때문에 조건을 따로 적지 않고 콜론(:)만 적는다.  

### if ~elif
조건이 여러개로 나눠질 경우 추가적인 조건에 대해 elif로 처리한다.  

```
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

```
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

```
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
```
참일경우 if 조건식 else 거짓일 경우
```
is_holiday에 True또는 False값을 할당한 후, if문과 조건표현식을 사용해서 각각 'Good'과 'Bad'를 출력하는 코드를 짜본다.
```
>>> is_holiday = True
>>> print('good') if is_holiday == True else print('bad')
good
 
>>> is_holiday = False
>>> print('good') if is_holiday == True else print('bad')
bad
```

### 중첩조건표현식
```
# 조건이 2개일 경우
조건1이 참일경우 if 조건1 else 조건1은 거짓이나 조건2가 참일경우 if 조건2 else 조건1,2가 모두 거짓일 경우
```
vacation에 1에서 10중 아무 값이나 할당 후, if, elif, else문과 중첩 조건표현식을 사용해서 각각 vacation이 7이상이면 'Good', 5이상이면 'Normal', 그 이하면 'Bad'를 출력하는 코드를 짜본다.
```
>>> vacation = 5
>>> print('Good') if vacation >= 7 else print('Normal') if vacation >= 5 else print('bad')
normal
```
