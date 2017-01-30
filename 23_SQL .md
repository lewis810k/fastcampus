# SQL

>SQL : 관계형 데이터베이스 관리 시스템(RDBMS)의 데이터를 관리하기 위해 설계된 특수 목적의 프로그래밍 언어이다.  
>[SQL - 위키백과](https://ko.wikipedia.org/wiki/SQL)

RDBS에서 데이터 처리는 RDBS용 데이터 모음인 테이블[2]을 기준으로 이루어지기 때문에 SQL 역시 테이블을 염두에 두고 읽으면 이해하기 쉽다.

### SQL이 할 수 있는 것들
- DB에 쿼리를 실행할 수 있다.
- DB의 데이터를 추출할 수 있다. 
- DB데이터의 추가, 수정, 삭제가 가능하다.
- 새로운 DB와 테이블을 생성할 수 있다.

### 구문

```sql
SELECT * FROM Customer;
```
위의 예제는 database에서 customer라는 테이블의 모든 요소를 출력하는 명령이다.  

**SQL은 대소문자를 특별히 구분하지 않는다.**  
SELECT와 select는 동일하게 취급된다.  

### 주요 명령어
- SELECT : DB로부터 데이터를 추출한다.  
- UPDATE : DB의 데이터를 수정한다.  
- DELETE - DB의 데이터를 삭제한다. 
- INSERT INTO - DB에 새로운 데이터를 추가한다. 
- CREATE DATABASE - 새로운 DB를 생성한다. 
- ALTER DATABASE - DB를 수정한다. 
- CREATE TABLE - 새로운 테이블을 생성한다. 
- ALTER TABLE - 기존 테이블을 수정한다. 
- DROP TABLE - 테이블을 삭제한다. 
- CREATE INDEX - 인덱스를 생성한다.  
- DROP INDEX - 인덱스를 삭제한다.  

-

### Select
DB로부터 데이터를 추출한다.

```sql
SELECT column_name, column_name   
FROM table_name

and

SELECT * FROM table_name;
```

구문에서도 예제로 설명했지만  
```sql
SELECT * FROM Customers;  
```

![select1](https://s30.postimg.org/ohn52g12p/image.png)  

Customers 테이블의 내용 전체가 출력된다.  
`*`는 all을 의미한다.   

Customers 테이블에는 `CustomerID, CustomerName, ContactName, Address, City, PostralCode, Country`라는 `Column이 존재`한다. 

전체 데이터가 91개이기 때문에 실제로는 화면에 보이는 7개가 아닌 91개가 모두 출력된다.  

```sql
SELECT CustomerName, City FROM Customers;
```
![select2](https://s30.postimg.org/63clygos1/image.png)

이번에는 all을 의미하는 `*`을 사용하지 않고 직접 column name을 입력했다.    
Customers 테이블에서 CustomerName, City 정보만 출력한다.

-

### Distinct

```sql
SELECT DISTINCT column_name,column_name
FROM table_name;
```
특정 column에서 중복되지 않은 정보만 추려서 보여준다.  

```sql
SELECT DISTINCT City FROM Customers;
```

![distinct](https://s28.postimg.org/tvxd2vy25/image.png)

기존 데이터는 91개였는데 69개라고 표시된 것은 중복된 city정보가 걸러졌기 때문이다.  
Mexico D.F.의 경우 2번, 3번 데이터가 중복되기 때문에 한번만 표시되었다. 

-

### Where clause	
주로 **특정 데이터만 지정**해서 출력할 때 사용한다.   
**특정 범위**를 지정하기도 한다. 

```sql
SELECT column_name,column_name
FROM table_name
WHERE column_name operator value;
```

```sql
SELECT * FROM Customers
WHERE Country='Mexico';
```
![where1](https://s28.postimg.org/6vur3qfb1/image.png)   
Customers의 데이터 중에서 Country 값이 'Mexico'인 데이터의 모든 컬럼정보(*)를 출력한다.

```sql
SELECT * FROM Customers
WHERE CustomerID=1;
```

![where2](https://s28.postimg.org/67lwksgl9/image.png)

CusomterID값이 1인 데이터의 모든 컬럼정보를 출력한다.  

-

### Operators in The WHERE Clause

where 안에 사용할 수 있는 연산자

Operator|Description
---|---
=		|Equal
<>		|Not equal. Note: In some versions of SQL this operator may be written as		|!=
>       |Greater than
<		|Less than
>=      |Greater than or equal
<=		|Less than or equal
BETWEEN	|Between an inclusive range
LIKE	|Search for a pattern
IN		|To specify multiple possible values for a column  


```sql
SELECT * FROM Customers
WHERE CustomerID <= 30;
```
CustomerID가 30이하인 데이터를 추출한다. 

-

### AND & OR Operators
하나 이상의 조건에 대한 데이터 필터 작업에 사용한다. 

- AND는 조건이 모두 부합해야한다.  
- OR는 제시된 조건들 중 하나라도 부합하면 된다.  
- 주로 where와 같이 사용한다. 

```sql
SELECT * FROM Customers
WHERE Country='Germany'
AND City='Berlin';
```
![and](https://s28.postimg.org/dwxtj2ux9/image.png)

Germany의 Berlin에 거주하는 사람에 대한 모든 컬럼 정보를 출력한다.  

```sql
SELECT * FROM Customers
WHERE City='Berlin'
OR City='München';
```

![or](https://s28.postimg.org/mt8ln0ljh/image.png)

Berlin이나 München에 거주하는 사람에 대한 모든 컬럼 정보를 출력한다.  

```sql
SELECT * FROM Customers
WHERE Country='Korea'
AND (City='Busan' OR City='Seoul');
```
`AND`와 `OR`를 혼합해서 사용할 수 있다. Korea라는 국가에 있으면서 Busan이나 Seoul에 거주하고 있는 사람의 정보를 출력하는 구문이다.  

-

### ORDER BY Keyword

```sql
SELECT column_name, column_name
FROM table_name
ORDER BY column_name ASC|DESC, column_name ASC|DESC;
```
특정 컬럼에 대해 오름차순, 혹은 내림차순으로 정렬한다. 


```sql
SELECT * FROM Customers
ORDER BY Country;
```
![order1](https://s24.postimg.org/potl3if05/image.png)

ORDER BY에 Column_name만 입력할 경우 기본값으로 오름차순으로 정렬이 된다.  

```sql
SELECT * FROM Customers
ORDER BY Country DESC;
```
![order2](https://s24.postimg.org/tmgusx1th/image.png)   
DESC라고 명시할 경우 해당 column에 대해 내림차순으로 정렬된다. 


```sql
SELECT * FROM Customers
ORDER BY Country ASC, CustomerName DESC;
```

![order3](https://s24.postimg.org/lv04ucxo5/image.png)  

복합적으로 사용할 경우 앞에 명시된 column에 대해 먼저 정렬이 된다.  
1. Country에 대해서 오름차순으로 정렬된다.  
2. Country정보가 같은 데이터끼리 CustomerName에 대해 내림차순으로 정렬된다.    

-

### INSERT INTO

새로운 데이터를 테이블에 추가한다.  

```sql
INSERT INTO table_name
VALUES (value1,value2,value3,...);
```
테이블에 value값을 순차적으로 입력하여 추가한다.  

```sql
INSERT INTO table_name (column1,column2,column3,...)
VALUES (value1,value2,value3,...);
```
테이블에 value값을 추가할 때, column과 맵핑하여 추가한다.  
예를 들면, value2는 column2에, value3은 column3에 추가된다.  

```sql
INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
VALUES ('Cardinal','Tom B. Erichsen','Skagen 21','Stavanger','4006','Norway');
```
![insert1](https://s29.postimg.org/ju0mymkg7/image.png)  
column과 value를 맵핑하여 추가하는 예제이다.

```sql
INSERT INTO Customers (CustomerName, City, Country)
VALUES ('Youngmin', 'Busan', 'Korea');
```
![insert2](https://s29.postimg.org/gynjrrgg7/image.png)  
column 내용을 모두 채우지 않을 경우 null값이 부여된다.  

> ###null
> 동사무소에서 사람 정보를 입력한다고 가정해보자. 주민등록번호라는 column이 있는데 이 정보는 반드시 채워져야 한다. 아무 값도 넣지 않는다고 해서 null이 입력되면 안된다. 꼭 필요한 정보가 아니라면 null을 기본값으로 대체하기도 한다.  
> ###ID   
>CustomerID값은 특별히 입력하지 않았는데 자동으로 다음 번호가 부여되고 있다. 사람의 이름과 주소 등은 중복될 경우가 있는데 유니크한 식별자를 통해 구분해야한다. 대게의 경우 ID값이 중복되지 않는 값으로 존재하고 자동으로 다음번호를 입력해준다. 이름도 중복되지 않기를 원할 경우 옵션을 줄 수 있다.(key 파트 참고)

-

### UPDATE

기존에 있던 데이터를 수정한다. 주로 데이터의 전체 내용을 수정하기 보다는 특정 column의 정보만 수정하는 경우가 많다. 

```sql
UPDATE table_name
SET column1=value1,column2=value2,...
WHERE some_column=some_value;
```

- UPDATE : 업데이트 할 테이블 이름  
- SET : 수정할 column과 새로운 value  
- WHERE : 특정 데이터 정보

> where clause는 수정하려고 하는 특정 데이터를 찾아주기 때문에 입력하지 않을 경우 해당 column의 전체 테이블 데이터가 수정된다. 반드시 기입해주어야 한다.  

```sql
UPDATE Customers
SET City='Hamburg'
WHERE CustomerID=1;
```  
기존 데이터  
![update1](https://s27.postimg.org/txu1jcn6r/image.png)

수정된 데이터   
![update2](https://s27.postimg.org/v1e5vb7tv/image.png)

Customers 테이블에서 CustomerID가 1인 데이터의 City값을 'Hamburg'로 수정한다.  

```sql
UPDATE Customers
SET ContactName='Alfred Schmidt', City='Frankfurt'
WHERE CustomerID=1;
```
한번에 여러 column의 값을 수정할수도 있다.  

```sql
UPDATE Customers
SET ContactName='Juan'
WHERE Country='Mexico';
```
ID는 유니크하기 때문에 1개의 row에 대해서 수정되지만  
where의 내용을 바꿔서 여러 row에 대해서 수정 가능하다.  

>마산 창원 진해는 각각이 시 단위였다. 하지만 2010년에 행정구역 통합에 따라 통합창원시가 되었고, 마산과 진해는 마산합포구, 마산회원구, 진해구로 변경되었다. 경상남도 진해시가 아니라 경상남도 창원시 진해구가 되었다. 데이터베이스에서 진해시로 되어있는 주소정보를 창원시로 변경하려면 WHERE address_si='진해시'를 선택하여 한번에 여러 row의 정보를 수정하는 것이 바람직하다.  
>
>하지만 WHERE를 명시하지 않을 경우 테이블 내에서 바뀌지 않아야 할 정보도 다 바뀌기 때문에 반드시 명시해야한다. 

-

### Delete
테이블의 특정 row를 삭제한다. 

```sql
DELETE FROM table_name
WHERE some_column=some_value;
```
테이블 내에서 WHERE를 통해 특정 row혹은 rows를 선택하여 삭제한다.    
>WHERE를 명시하지 않을 경우 테이블의 모든 정보가 삭제된다. 

```sql
DELETE FROM Customers
WHERE CustomerName='홍길동'
```
Customers 테이블에서 CustomerName이 '홍길동'인 row를 삭제한다.  

```sql
DELETE FROM Customers
WHERE CustomerName='홍길동' AND City='서울';
``` 
Customers 테이블에서 **서울에 사는 홍길동**의 정보를 삭제한다.  

```sql
DELETE * FROM Customers;
```
Customers 테이블의 모든 정보를 삭제한다. 되돌리기가 불가능하니 신중하게 해야한다.  

