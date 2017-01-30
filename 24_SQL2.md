### JOIN

두 개 이상의 테이블에서 데이터를 병합할 때 사용한다.  

### INNER JOIN Keyword
테이블간에 column 값이 하나라도 일치하는 모든 정보를 보여준다.  

```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name=table2.column_name;
```   
table1과 table2에서 column값이 같은 row를 출력한다. 

>INNER JOIN대신 JOIN이라고 써도 결과는 같다. 

![inner](http://www.w3schools.com/sql/img_innerjoin.gif)


```sql
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
INNER JOIN Orders
ON Customers.CustomerID=Orders.CustomerID
ORDER BY Customers.CustomerName;
```

- CustomerName과 OrderID를 보여준다.  
- Customers 테이블과 Orders 테이블을 비교한다.  
- 두 테이블의 CustomerID가 같은 정보를 찾는다.  
- CustomerName 기준으로 오름차순으로 정렬한다.  

-

### LEFT JOIN Keyword

INNER JOIN이 table1과 table2 모두 매칭되는 값을 출력했다면 LEFT JOIN은 INNER JOIN의 값에 더하여 table1에는 있지만 table2에는 없는 row도 출력한다.

```sql
SELECT column_name(s)
FROM table1
LEFT JOIN table2
ON table1.column_name=table2.column_name;
```

![leftjoin](http://www.w3schools.com/sql/img_leftjoin.gif)

```sql
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
LEFT JOIN Orders
ON Customers.CustomerID=Orders.CustomerID
ORDER BY Customers.CustomerName;
```

![leftjoin2](https://s30.postimg.org/m8xud3k6p/image.png)

구문 자체는 INNER JOIN과 비슷하다. LEFT JOIN으로 쿼리문을 작성할 경우 매칭되지 않은 결과의 경우 null값으로 처리되는 것을 볼 수 있다.  

-

### RIGHT JOIN Keyword

LEFT JOIN과 반대로 table2에는 있지만 table1에는 없는 정보도 출력한다. 

```sql
SELECT column_name(s)
FROM table1
RIGHT JOIN table2
ON table1.column_name=table2.column_name;
```

![rightjoin](http://www.w3schools.com/sql/img_rightjoin.gif)


-

### FULL OUTER JOIN Keyword
LEFT JOIN과 RIGHT JOIN을 합친 결과를 출력한다. 즉, table1에는 있지만 table2에는 없는 경우, 그리고 반대의 경우 모두 출력한다.  

```sql
SELECT column_name(s)
FROM table1
FULL OUTER JOIN table2
ON table1.column_name=table2.column_name;
```

![fulljoin](http://www.w3schools.com/sql/img_fulljoin.gif)

```sql
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
FULL OUTER JOIN Orders
ON Customers.CustomerID=Orders.CustomerID
ORDER BY Customers.CustomerName;
```
![fulljoin2](https://s28.postimg.org/jpgt9jkml/image.png)

그림과 같이 table1이나 table2의 데이터가 매칭되지 않아도 출력결과에 포함한다.  

-

### UNION Operator

두 개 이상의 SELECT 쿼리문을 합쳐서 보여준다.   
기본적으로 중복되는 값을 하나로 처리해준다.  
SELECT 쿼리문에 포함되는 column은 서로 동일해야한다.  

```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

```sql
SELECT City FROM Customers
UNION
SELECT City FROM Suppliers
ORDER BY City;
```

첫번째 SELECT의 결과와 두번째 SELECT의 결과를 합쳐서 보여준다. 

```sql
SELECT City FROM Customers
UNION ALL
SELECT City FROM Suppliers
ORDER BY City;
```

**UNION ALL**을 사용하면 중복되는 데이터를 하나로 처리하지 않고 모두 보여준다.  

-

### SELECT INTO

테이블에서 데이터를 복사하여 새로운 테이블에 추가한다.  

```sql
SELECT *
INTO newtable [IN externaldb]
FROM table1;
```
table1의 모든 데이터를 newtable에 추가한다.  

```sql
SELECT *
INTO CustomersBackup2013
FROM Customers;
```

Customers의 모든 데이터를 CustomersBackup2013 테이블에 추가한다.  

```sql
SELECT *
INTO CustomersBackup2013 IN 'Backup.mdb'
FROM Customers;
```
Backup이라는 다른 데이터베이스에 포함된 테이블로 데이터를 추가할 수 있다.  

```sql
SELECT CustomerName, ContactName
INTO CustomersBackup2013
FROM Customers;
```
특정 column만 지정하기도 한다.  

-

### SQL INSERT INTO SELECT

```sql
INSERT INTO table2
SELECT * FROM table1;
```

table1에서 데이터를 SELECT하여 table2에 추가한다.  

```sql
INSERT INTO Customers (CustomerName, Country)
SELECT SupplierName, Country FROM Suppliers;
```

- Suppliers 테이블에서 SupplierName과 Country라는 column을 선택한다.  
- Customers 테이블의 CustomerName, Country라는 Column에 해당 데이터를 각각 추가한다.  

-

### CREATE DATABASE
```sql
CREATE DATABASE my_db;
```

my_db라는 데이터베이스를 생성한다.  

-

### CREATE TABLE

```sql
CREATE TABLE table_name
(
column_name1 data_type(size),
column_name2 data_type(size),
column_name3 data_type(size),
....
);
```
테이블을 생성하면서 column정보를 동시에 추가할 수 있다.  

```sql
CREATE TABLE Persons
(
PersonID int,
LastName varchar(255),
FirstName varchar(255),
Address varchar(255),
City varchar(255)
);
```
Persons라는 테이블을 만들고  
PersonID, LastName, FirstName, Address, City라는 column을 만든다.  
각 column은 내부적으로 다루게 되는 데이터 타입을 지정할 수 있다.  
varchar의 경우 문자열의 길이를 괄호안에 지정해준다.  

-

### Constraints

각 column에 추가적인 옵션을 부가한다. 주로 제약조건에 해당된다.  
```sql
CREATE TABLE table_name
(
column_name1 data_type(size) constraint_name,
column_name2 data_type(size) constraint_name,
column_name3 data_type(size) constraint_name,
....
);
```

테이블 생성시 column 데이터 타입 옆에 기록한다.  

꼭 지정해주어야 하는 값, 중복되면 안 되는 값 등의 옵션들이 있다.  

-

### NOT NULL

```sql
CREATE TABLE PersonsNotNull
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
```

P_Id와 LastName은 NOT NULL이라는 constraint가 부여되었다.  
다른 column들의 경우 아무 값도 입력하지 않으면 자동적으로 null로 저장되지만 P_Id와 LastName은 null로 저장될 수 없다. 아무 값도 입력하지 않으면 에러를 발생시키고 쿼리문을 테이블에 반영하지 않는다.  

-

### UNIQUE

```sql
CREATE TABLE Persons
(
P_Id int NOT NULL UNIQUE,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
```
P_Id에 UNIQUE constraint가 지정될 경우 해당 column의 데이터는 서로 중복되지 않도록 한다. 이미 존재하는 데이터를 추가하려고 할 경우 에러를 발생시켜 쿼리문을 반영시키지 않는다.  

테이블 생성 시점에 UNIQUE를 추가하지 않았을 경우 쿼리문으로 지정할 수 있다.
```sql
ALTER TABLE Persons
ADD UNIQUE (P_Id)
```
P_Id column에 UNIQUE constraint를 부여한다.  

```sql
ALTER TABLE Persons
DROP UNIQUE (P_Id)
```
부여된 UNIQUE를 삭제할 경우 DROP 명령어를 사용한다.  

-

### PRIMARY KEY
PRIMARY KEY는 반드시 UNIQUE한 값이어야 하며 NULL이 될 수 없다. 테이블은 Primary Key를 하나만 가져야 한다. 

```sql
CREATE TABLE Persons
(
P_Id int NOT NULL PRIMARY KEY,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
```

지정하는 방식은 다른 CONSTRAINT의 방식과 동일하다. P_Id를 PRIMARY KEY로 지정한다. 

```sql
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName)
)
```

PRIMARY KEY는 여러 column을 복합적으로 사용할 수 있다. 위에서 하나만 가져야 한다고 했다. 이 예제에서는 P_Id와 LastName이 각각 PRIMARY KEY가 아니라 P_Id와 LastName을 합쳐서 하나의 KEY로 본다. LastName은 다른 row와 중복될 수 있지만 P_Id와 합쳐진 LastName은 KEY의 성격을 가질 수 있다. 

```sql
ALTER TABLE Persons
ADD PRIMARY KEY (P_Id)

ALTER TABLE Persons
DROP PRIMARY KEY (P_Id)
```
쿼리문을 통해 PRIMARY KEY를 추가하거나 삭제할 때 ADD와 DROP을 사용한다. 

-

### FOREIGN KEY
FOREIGN KEY는 다른 테이블의 PRIMARY KEY를 가리킨다.  

table1에서 PRIMARY KEY로 사용되는 값은 UNIQUE하다.  
table2에서 table1의 PRIMARY KEY를 참조하는 것이 FOREIGN KEY이다. 
테이블 간 서로 영향을 주기 때문에 값의 변동을 막아 연결고리가 끊어지지 않게 한다.  

```sql
CREATE TABLE Orders
(
O_Id int NOT NULL PRIMARY KEY,
OrderNo int NOT NULL,
P_Id int FOREIGN KEY REFERENCES Persons(P_Id)
)
```

테이블을 생성하면서 FOREIGN KEY를 지정할 수 있다.   
현재 테이블의 P_Id column을 Persons 테이블의 P_Id column을 참조하도록 한다.  

```sql
ALTER TABLE Orders
ADD FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)

ALTER TABLE Orders
DROP FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
```
다른 CONSTRAINT와 마찬가지로 ADD와 DROP으로 테이블 생성시점이 아닐 때 수정할 수 있다. 

-

### CHECK
column에 입력될 데이터에 조건을 걸어 입력될 값을 제한한다.  

```sql
CREATE TABLE Persons
(
P_Id int NOT NULL CHECK (P_Id>0),
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
```
P_Id에 CHECK constraint를 지정하고 조건으로 (P_Id > 0)를 정한다.   
P_Id column에 0이하의 값이 들어갈 경우 에러를 발생시켜 테이블에 반영되지 않도록 한다.  

```sql
ALTER TABLE Persons
ADD CHECK (P_Id>0)

ALTER TABLE Persons
DROP CHECK (P_Id>0)
```
다른 CONSTRAINT와 마찬가지로 ADD와 DROP으로 테이블 생성시점이 아닐 때 수정할 수 있다. 

-

### DEFAULT 
DEFAULT로 지정된 column의 경우 아무 값도 입력되지 않을 경우 초기값을 지정하여 자동으로 입력된다.  

```sql
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255) DEFAULT 'Sandnes'
)
```

City column에 DEFAULT가 지정되었고 그 값은 'Sandnes'이다.  
Sandnes라는 입력이 자주 발생될 경우 DEFAULT로 지정하여 추가적인 입력 작업을 방지할 수 있다. 

```sql
CREATE TABLE Orders
(
O_Id int NOT NULL,
OrderNo int NOT NULL,
P_Id int,
OrderDate date DEFAULT GETDATE()
)
```
이 경우에는 GETDATE()라는 함수를 사용했다. 현재 시간을 불러오는 함수를 사용하여 주문이 발생할 경우 자동적으로 주문시간을 입력할 수 있게 한다.  

```sql
ALTER TABLE Persons
ALTER COLUMN City SET DEFAULT 'SANDNES'

ALTER TABLE Persons
ALTER COLUMN City DROP DEFAULT
```
추가와 삭제는 다른 명령어의 형태와 유사하다.  

-

### CREATE INDEX
INDEX를 생성한다. INDEX는 데이터를 검색할 때 테이블 전체를 탐색하지 않고 빠르고 효율적으로 찾게 해준다.  

```sql
CREATE INDEX PIndex
ON Persons (LastName)
```
Persons 테이블의 LastName column에 대해 PIndex라는 이름의 INDEX를 생성한다.  

```sql
CREATE UNIQUE INDEX PIndex
ON Persons (LastName)
```

UNIQUE INDEX는 데이터의 중복이 발생되지 않도록 한다.  

```sql
CREATE INDEX PIndex
ON Persons (LastName, FirstName)
```
여러 column의 조합을 이용하여 INDEX를 생성할 수 있다. 

-

### DROP

삭제기능을 담당한다.  

```sql
- INDEX
DROP INDEX table_name.index_name

- TABLE
DROP TABLE table_name

- DATABASE
DROP DATABASE database_name
```

### ALTER
column에 대해 추가, 삭제, 수정을 할 수 있다.  

```sql
ALTER TABLE Persons
ALTER COLUMN DateOfBirth year
```
DateOfBirth column의 데이터타입을 year로 수정한다.  

```sql
ALTER TABLE Persons
DROP COLUMN DateOfBirth
```

DateOfBirth column을 삭제한다. 

-

### AUTO INCREMENT
말 그대로 값을 자동적으로 증가시켜준다.  
ID의 경우 이전에 있던 값과 항상 다른 값을 유지해야 UNIQUE의 성격을 가질 수 있다. 추가되는 row에 대해 계속해서 증가된 값을 부여한다.  

```sql
CREATE TABLE Persons
(
ID int NOT NULL AUTO_INCREMENT,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
PRIMARY KEY (ID)
)
```
AUTO_INCREMENT로 지정된 ID는 초기값으로 1부터 시작된다. 추가되는 데이터의 경우 ID를 자동적으로 1씩 증가된 값을 부여받는다. 

```sql
ALTER TABLE Persons AUTO_INCREMENT=100
```

다른 값을 부여하면 자동적으로 증가되는 수치를 변경할 수 있다. 

-

### VIEWS


-

### DATES

#### SQL Server Date Functions

Function	|Description
---|---
GETDATE()	|현재 날짜와 시간 반환
DATEPART()	|날짜 시간의 일부를 반환
DATEADD()	|날짜에 시간을 추가하거나 뺀다.
DATEDIFF()	|두 날짜의 차이를 반환
CONVERT()	|날짜 시간 데이터를 다른 형태로 보여준다. 

날짜 데이터에 시간을 포함하지 않아야 정렬하거나 검색할 때 유용해진다.  

OrderDate라는 column에 다음과 같은 데이터를 가진다고 해보자.  

```
OrderDate

2008-11-11   
2008-11-09
2008-11-11   
2008-11-29
```

```sql
SELECT * FROM Orders WHERE OrderDate='2008-11-11'
```
이 쿼리문을 입력할 경우 OrderDate의 값이 2008-11-11인 경우만 보여진다.  

하지만 OrderDate에 다음과 같이 시간 값을 경우  
```
OrderDate

2008-11-11 13:23:44
2008-11-09 12:22:15
2008-11-11 18:09:21
2008-11-29 19:01:02
```

위의 쿼리문을 발생시켜도 아무런 데이터가 출력되지 않는다. 매칭이 되지 않기 때문이다.

-

### NULL Values

- 아무런 값도 지정되지 않을 경우 NULL 데이터가 자동적으로 입력된다.  
- NULL은 0과 다르다. 0은 0이라는 데이터가 입력된 상태이다. 
- NULL은 비교연산이 불가하고 IS NULL이나 IS NOT NULL 연산을 사용한다.  

```sql
SELECT LastName,FirstName,Address FROM Persons
WHERE Address IS NULL
```

WHRER Address IS NULL은 Address column에 NULL value를 가진 row를 선택해준다.  

```sql
SELECT LastName,FirstName,Address FROM Persons
WHERE Address IS NOT NULL
```

반대로, IS NOT NULL은 NULL value를 가지지 않은 row를 선택해준다. 즉, 값이 입력된 경우를 말한다.  

-

### Data Types

column마다 하나의 데이터 형태를 가질 수 있으며 항상 명시해주어야 한다.  

잘 정리된 자료는 이 링크를 통해 확인할 수 있다.   

- [GENERAL DATA TYPES](http://www.w3schools.com/sql/sql_datatypes_general.asp)
- [DB DATA TYPES](http://www.w3schools.com/sql/sql_datatypes.asp)

-

### COMMENTS

```sql
--Select all:
SELECT * FROM Customers;

SELECT * FROM Customers -- WHERE City='Berlin';
```
`--` 기호 뒤에 있는 내용은 모두 주석으로 처리되어 쿼리문에 반영되지 않는다.  

 
```sql
/*Select all the columns
of all the records
in the Customers table:*/
SELECT * FROM Customers;

SELECT CustomerName, /*City,*/ Country FROM Customers;
```

`/*  */`사이에 있는 내용 모두 주석으로 처리할 수 있다. 



