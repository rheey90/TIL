#SQL

###Intro

SQL은 데이터베이스를 접근하고 조작하는 표준 언어이다

SQL -> Structured Query Language

1. 데이터베이스로부터 query를 실행할 수 있다.
2. 데이터베이스로부터 데이터를 검색할 수 있다.
3. 데이터베이스에 기록을 삽입할 수 있다.
4. 데이터베이스 기록을 업데이트 할 수 있다.
5. 데이터베이스 기록을 삭제할 수 있다.
6. 새로운 데이터베이를 만들 수 있다.
7. 데이터베이스에 새로운 테이블을 만들 수 있다.
8. 데이터베이스에 저장 절차를 만들 수 있다.
9. 데이터베이스에 뷰를 만들 수 있다.
10. 테이블, 절차, 뷰 등에 접근 허가를 설정할 수 있다.

데이터베이스로부터 데이터를 보여줄 수 있는 웹 사이트를 만들려면 RDBMS 데이터베이스 프로그램, PHP나 ASP 등과 같은 서버 사이드 언어, 얻고자 하는 데이터를 접근하기 위한 SQL, HTML/CSS 등을 알고있어야 한다.



RDBMS -> Relational Database Management System

RDBMS에 저장 되어는 데이터베이스 객체는 테이블라고 불린다. 행은 테이블에 있는 각각의 데이터를 포함하고 열은 데이터의 속성과 관련있는 자료들을 보여준다.



### SQL Syntax

데이터베이스는 한 개 이상의 테이블을 가지고 있다. 각 테이블은 명칭으로 식별 된다.

- SELECT: 데이터베이스로부터 데이터를 추출
- UPDATE: 데이터베이스에 데이터를 업데이트
- DELETE: 데이터베이스에서 데이터를 삭제
- INSERT INTO: 데이터베이스에 새 데이터를 삽입
- CREATE DATABASE: 새 데이터베이스를 만듬
- ALTER DATABASE: 데이터베이스를 수정
- DROP TABLE: 테이블을 삭제
- CREATE INDEX: 인덱스를 만듬
- DROP INDEX: 인덱스 삭제



###SQL SELECT statement

SELECT는 데이터베이스로부터 데이터를 고를 때 사용된다.

```SQL
SELECT column1, column2, ...
FROM table_name;
```

테이블로 부터 열1, 열2을 가져옴

```SQL
SELECT * FROM table_name;
```

테이블에 있는 모든 필드를 선택



Customers 테이블

![스크린샷 2019-08-19 오후 1.28.25](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.28.25.png)

```sql
SELECT CustomerName, City FROM Customers;
```

Customers 테이블에서 CustomerName와 City를 선택

```sql
SELECT * FROM Customers;
```

Customers 전체 테이블 선택



### SQL SELECT DISTINCT statement

SELECT DISTINCT는  중복되지 않은 특정 값만 불러온다.

```sql
SELECT DISTINCT column1, column2, ...
FROM table_name;
```



Customers 테이블

![스크린샷 2019-08-19 오후 1.28.25](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.28.25.png)

```sql
SELECT Country FROM Customers;
```

위와 같이 실행하면 같은 값을 갖고 있는 항목들이 중복되어 불려와 총 91개의 값들을 반환하지만

```SQL
SELECT DISTINCT Country FROM Customers;
```

이렇게 하면 중복되는 값들을 제외한 고유한 값만 가져오기 때문에 총 21개의 값들을 반환한다.

```sql
SELECT COUNT(DISTINCT Country) FROM Customers;
```

고유한 값의 개수를 가져온다.



### SQL WHERE clause

WHERE은 특정한 조건을 만족하는 데이터를 필터하기 위해 사용된다.

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```



Customers 테이블

![스크린샷 2019-08-19 오후 1.28.25](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.28.25.png)

```sql
SELECT * FROM Customers
WHERE Country='Mexico';
```

Customers 테이블에서 Mexico 값을 가지고 있는 항목들만 불러옴.

문자열은 사용해 WHERE을 쓰고 싶을 때는 조건에다 따옴표를 써야하지만 숫자는 그렇지 않다.

```sql
SELECT * FROM Customers
WHERE CustomerID=1;
```

Customers 테이블에서 CustomerID가 1인 값만 불러옴.



![스크린샷 2019-08-19 오후 1.50.21](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.50.21.png)

WHERE을 사용할 때 위와 같은 조건문들을 사용할 수 있다.



### SQL AND, OR and NOT operators

WHERE은 AND, OR, NOT과 섞어 쓸 수 있다.

- AND -> 모든 조건이 참이 것만 불러옴.
- OR -> 조건 중에 하나라도 참이면 불러옴.
- NOT -> 조건이 거짓이 항목을 불러옴.

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 AND condition3;

SELECT column1, column2, ...
FROM table_name
WHERE condition1 OR condition2 OR condition3;

SELECT column1, column2, ...
FROM table_name
WHERE NOT condition;
```



Customers 테이블

![스크린샷 2019-08-19 오후 1.28.25](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.28.25.png)

```sql
SELECT * FROM Customers
WHERE Country='Germany' AND City='Berlin';
```

Country가 Germany이고 City가 Berlin인 데이터만 가져옴.

```sql
SELECT * FROM Customers
WHERE Country='Germany' OR Country='Spain';
```

Country가 Germany이거나 Spain인 데이터를 가져옴.

```sql
SELECT * FROM Customers
WHERE NOT Country='Germany';
```

Country가 Germany가 아닌 데이터만 가져옴.

```sql
SELECT * FROM Customers
WHERE Country='Germany' AND (City='Berlin' OR City='München');
```

Country가 Germany이면서 도시가 Berlin 혹은 München인 데이터만 가져옴. 복잡한 조건문을 쓸 땐 괄호를 활용한다.

```sql
SELECT * FROM Customers
WHERE NOT Country='Germany' AND NOT Country='USA'
```

Country가 Germany하고 USA가 아닌 데이터를 가져옴.



### SQL ORDER BY keyword

ORDER BY는 결과 값들을 오름차순 혹은 내림차순으로 정리한다.

```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2 ... ASC|DESC;
```

ORDER BY는 디폴트로 오름차순으로 분류하고 내름차순으로 정렬할 시 DESC를 사용하면 된다.



Customers 테이블

![스크린샷 2019-08-19 오후 1.28.25](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.28.25.png)

```sql
SELECT * FROM Customers
ORDER BY Country;
```

Country를 기준으로 오름차순으로 분류함.

```sql
SELECT * FROM Customers
ORDER BY Country DESC;
```

Country를 기준으로 내림차순으로 분류함.

```sql
SELECT * FROM Customers
ORDER BY Country, CustomerName;
```

먼저 Country를 분류하고 Country가 같으면 CustomerName 기준으로 분류함.

```sql
SELECT * FROM Customers
ORDER BY Country ASC, CustomerName DESC;
```

Country를 오름차순으로 분류한 후 CustomerName을 내림차순으로 분류함.



### SQL INSERT INTO statement

INSERT INTO는 새 항목을 테이블에 삽입할 때 쓰인다.

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

열 이름과 값들을 직접 지정하여 삽입.

```sql
INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```

모든 열에 값을 추가하는 경우라면 열 이름을 지정하지 않아도 된다.



Customers 테이블

![스크린샷 2019-08-19 오후 2.25.53](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 2.25.53.png)

```sql
INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');
```

![스크린샷 2019-08-19 오후 2.37.20](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 2.37.20.png)



```sql
INSERT INTO Customers (CustomerName, City, Country)
VALUES ('Cardinal', 'Stavanger', 'Norway');
```

![스크린샷 2019-08-19 오후 2.38.28](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 2.38.28.png)



### SQL NULL values

특정 필드에 값이 필요하지 않다면 NULL 값으로 저장될 수 있다.

Null 값은 비교 연산자가 사용될 수 없다.

```sql
SELECT column_names
FROM table_names
WHERE column_name IS NULL;

SELECT column_names
FROM table_name
WHERE column_names IS NOT NULL;
```



Customers 테이블

![스크린샷 2019-08-19 오후 1.28.25](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.28.25.png)

```sql
SELECT CustomerName, ContactName, Address
FROM Customers
WHERE Address IS NULL;
```

Customers 테이블로부터 CustomerName, ContactName, Address 열에 NULL 값을 지닌 항목을 반환.

```sql
SELECT CustomerName, ContactName, Address
FROM Customers
WHERE Address IS NOT NULL;
```

Customers 테이블로부터 CustomerName, ContactName, Address 열에 NULL 값을 지니지 않은 항목을 반환.



### SQL UPDATE statement

UPDATE는 이미 존재하는 항목들을 수정하는데 쓰인다.

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```



Customers 테이블

![스크린샷 2019-08-19 오후 1.28.25](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.28.25.png)

```sql
UPDATE Customers
SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
WHERE CustomerID = 1;
```

CustomerID가 1인 항목을 SET에 있는 값들로 수정.

![스크린샷 2019-08-19 오후 2.48.40](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 2.48.40.png)

```sql
UPDATE Customers
SET ContactName = 'Juan'
WHERE Country = 'Mexico';
```

Country가 Mexico인 항목들에 ContactName을 Juan으로 수정.

![스크린샷 2019-08-19 오후 2.50.16](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 2.50.16.png)

UPDATE를 사용할 땐 WHERE을 항상 지정할 것을 권장.



### SQL DELETE statment

DELETE는 테이블에서 항목을 삭제할 때 쓰인다.

```sql
DELETE FROM table_name WHERE condition;
```



CustomerID가 1인 항목을 SET에 있는 값들로 수정.

![스크린샷 2019-08-19 오후 2.48.40](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 2.48.40.png)

```sql
DELETE FROM Customers WHERE CustomerName = 'Alfreds Futterkiste';
```

Customers 테이블에서 CustomerName이 Alfreds Futterkiste인 항목을 삭제.

![스크린샷 2019-08-19 오후 2.54.03](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 2.54.03.png)

```sql
DELETE FROM table_name;
```

테이블을 통째로 삭제.



### SQL Wildcards

Wildcard 문자열에서 하나 이상의 character를 바꿀 때 사용된다.

![스크린샷 2019-08-19 오후 2.56.43](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 2.56.43.png)



Customers 테이블

![스크린샷 2019-08-19 오후 1.28.25](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 1.28.25.png)

```sql
SELECT * FROM Customers
WHERE City LIKE 'ber%'
```

City에서 ber로 시작하는 항목들을 반환.

```sql
SELECT * FROM Customers
WHERE City LIKE '%es%';
```

City에서 es를 가진 항목들을 반환.

```sql
SELECT * FROM Customers
WHERE City LIKE `_ondon';
```

City에서 ondon으로 시작하는 항목들을 반환.

```sql
SELECT * FROM Customers
WHERE City LIKE 'L_n_on';
```

City에서 L로 시작하고 n 그리고 on을 가진 항목들을 반환.

```sql
SELECT * FROM Customers
WHERE City LIKE '[bsp]%';
```

City에서 b, s, p로 시작하는 항목들을 반환.

```sql
SELECT * FROM Customers
WHERE City LIKE '[a-c]%';
```

City에서 a, b, c로 시작하는 항목들을 반환. ! 혹은 NOT LIKE 사용하여 그렇지 않은 값들을 반환하는 것도 가능.

### SQL Aliases

SQL aliase는 테이블,열에 임시적 이름을 주기 위하여 쓰인다. Alias은 query를 사용하는 동안에만 적용된다.

```sql
SELECT column_name AS alias_name
FROM table_name

SELECT column_name(s)
FROM table_name AS alias_name
```



![스크린샷 2019-08-19 오후 3.09.01](/Users/damagedcase/Documents/Code States/IM14/TIL/SQL/스크린샷 2019-08-19 오후 3.09.01.png)

```sql
SELECT CustomerID AS ID, CustomerName AS Customer
FROM Customers;
```

CustomerID를 ID로 그리고 CustomerName을 Customer로 임시적으로 바꿈.

```sql
SELECT CustomerName AS Customer, ContactName AS [Contact Person]
FROM Customers;
```

CustomerName을 Customer으로 그리고 ContanctName을 Contact Person로 임시적으로 바꿈.

```sql
SELECT CustomerName, Address + ', ' + PostalCode + ' ' + City + ', ' + Country AS Address
FROM Customers;
```

Address를 Address, PostalCode, City, Country로 합쳐서 임시적으로 바꿈.

