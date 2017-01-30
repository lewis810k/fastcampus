# SQL FUNCTIONS

### AVG()

```sql
SELECT AVG(column_name) FROM table_name
```

table에서 특정 column을 선택하여 전체 데이터의 평균값을 반환해준다. 

```sql
SELECT AVG(Price) FROM Products;
```

AVG(Price)|
---|
28.866363636363637|

Products column의 모든 데이터의 평균을 출력해준다.  

```sql
SELECT AVG(Price) AS PriceAverage FROM Products;
```

PriceAverage|
---|
28.866363636363637|

Column 이름을 변경할 수 있다.  

```sql
SELECT ProductName, Price FROM Products
WHERE Price>(SELECT AVG(Price) FROM Products);
```

WHERE 구문에서의 SELECT는 위에서 살펴보았다. 28.87이라는 값이 평균 값으로 출력되었다. 이 쿼리문은 Price > 28.87인 row의 ProductName, Price column 정보를 출력해준다.  

-

### COUNT()

```sql
SELECT COUNT(*) AS NumberOfOrders FROM Orders;
```

NumberOfOrders|
---|
196|

Orders 테이블의 데이터 수를 출력해준다.  

```sql
SELECT COUNT(column_name) FROM table_name;
```

column 정보를 입력할 경우 해당 column의 데이터 수를 출력해준다. 이 때 null value는 카운트되지 않는다. 

-

### First() & Last()

First()는 첫번째 데이터를 반환해준다.  
```sql
SELECT FIRST(CustomerName) AS FirstCustomer FROM Customers;
```

SQL server에서는 `FIRST()` 대신에 `SELECT TOP 1`을 사용하여 최상단 데이터를 출력할 수 있다. 

Last()는 마지막 데이터를 반환해준다.  
`SELECT TOP 1`을 사용할 때 `ORDER BY` 옵션으로 `DESC`을 지정할 경우 마지막 데이터를 출력할 수 있다. 

-

### Max() & Min()

Max()는 특정 column의 최대값을 출력한다. 

```sql
SELECT MAX(column_name) FROM table_name;
```

```sql
SELECT MAX(Price) AS HighestPrice FROM Products;
```

HighestPrice|
---|
263.5|

Min()은 특정 column의 최소값을 출력한다. 
동작은 Max()와 동일하다. 

-

### Sum()

```sql
SELECT SUM(column_name) FROM table_name;
```

특정 column의 모든 데이터의 합을 출력한다.  

-

### Group By

### Having 

### Ucase()
데이터를 대문자로 출력해준다.   
```sql
SELECT UCASE(column_name) FROM table_name;
```

### Lcase()
데이터를 소문자로 출력해준다. 
```sql
SELECT LCASE(column_name) FROM table_name;
```

-

### Mid()
```sql
SELECT MID(column_name,start,length) AS some_name FROM table_name;
```
특정 column에서 문자열을 지정한다. 해당 문자열의 몇번째 인덱스에서 시작할지(start)를 정하고 start 지점부터 얼마나 추출할지(length)를 정한다. 
 
```sql
SELECT MID(City,2,4) AS ShortCity
FROM Customers;
```

![mid](https://s23.postimg.org/xqsnqn5i3/image.png)

erli은 원래 문자열이 Berlin이다.  
start 지점이 2이기 때문에 e부터 시작하고 length가 4이기 때문에 erli 4개의 문자를 출력한다.  

-

### Len()

```sql
SELECT LEN(column_name) FROM table_name;
```
문자열의 길이를 반환한다.  

위의 예제에서 City column이 선택되었다면 Berlin은 길이값인 6을 반환한다.  

-

### Round()

```sql
SELECT ROUND(column_name,decimals) FROM table_name;
```

반올림된 값을 반환한다.  

```sql
SELECT ProductName, ROUND(Price,2) AS RoundedPrice
FROM Products;
```

ROUND(price, 2)는 각 데이터를 소수 둘째자리까지 나타내기 때문에 셋째자리에서 반올림한 값이다. 

-

### Now()

```sql
SELECT NOW() FROM table_name;
```

현재 날짜와 시간 값을 
```
1/30/2017 12:04:26 PM 
```
형태로 반환한다.  

-

### Format()

데이터의 표시형식을 지정한다.  

```sql
SELECT ProductName, Price, FORMAT(Now(),'YYYY-MM-DD') AS PerDate
FROM Products;
```

Now()는 날짜와 시간 값을 다 보여주는 것이 디폴트로 설정되어있다. 이 설정을 'YYYY-MM-DD' 년월일 형식으로 변경해주는 작업을 Format 함수가 해준다.  

![format](https://s24.postimg.org/up9n4hmqt/image.png)



 




