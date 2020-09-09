# :card_index_dividers: SQL문 문법 조작

### SELECT

특정 테이블을 반환한다.

```sql
SELECT * FROM table;
```



### 테이블 생성

- contraints : PRIMARY KEY, NOT NULL, UNIQUE, DEFAULT 등

```sql
CREATE TABLE table (
	column1 datatype [constraints],
	column1 datatype [constraints],
);
```

<img width="814" alt="_2020-09-05__9 31 14" src="https://user-images.githubusercontent.com/60081217/92622346-e4f40100-f2ff-11ea-92f3-ca05b1a70d0b.png">

- 실제 예시

```sql
sqlite> CREATE TABLE classmates (
	id INTEGER PRIMARY KEY AUTOINCREMENT,
	name TExT NOT NULL,
	age INTEGER,
	address TEXT
);
```



### 테이블 삭제

```sql
DROP TABLE table;
```



### 추가

특정 테이블에 새로운 행을 추가하여 데이터를 추가

```sql
INSERT INTO table (column1, ...) VALUES (value1, ...);

sqlite > INSERT INTO classmates (name, age) VALUES ('홍길동', 23);

# 모든 열에 데이터를 넣을 때에는 column을 명시할 필요가 없음
sqlite > INSERT INTO classmates VALUES (2, '홍길동', 30, '서울');
```



### 읽기

특정 레코드를 조회

```sql
SELECT * FROM table WHERE condition;
SELECT comlunm1 FROM table WHERE condition;
```



### 삭제

```sql
DELETE FROM table WHERE condition;
```



### 수정

특정 테이블에 특정 레코드를 수정

```sql
UPDATE table SET column1=value1, ... WHERE condition;

sqlite> UPDATE classmates SET name='홍길동', address='제주' WHERE id=4;
```



### 기본 구문

```sql
SELECT <column> FROM <table>
[WHERE <condition>]
[GROUP BY <condition>]
[ORDER BY <column [ASC/DESC]>]
[LIMIT <integer>]
```



### DISTINCT

```sql
SELECT DISTINCT column FROM table;
```

특정 테이블에 특정 레코드의 개수

```sql
SELECT COUNT(column) FROM table;
SELECT MAX(column) FROM table;
SELECT MIN(column) FROM table;
SELECT SUM(column) FROM table;
SELECT AVG(column) FROM table;
```



### WHERE

```sql
SELECT column FROM table WHERE condition;

sqlite> SELECT * FROM classmates WHERE name='김' and age >= 19
```



### LIKE

```sql
SELECT * FROM table WHERE column LIKE 'pattern';

sqlite> SELECT * FROM classmates WHERE phone LIKE '010-%';
```

<img width="842" alt="_2020-09-05__9 55 00" src="https://user-images.githubusercontent.com/60081217/92622362-e8878800-f2ff-11ea-995f-4930eec5f8e6.png">



### ORDER BY

```sql
SELECT columns FROM table ORDER BY column1, column2 ASC|DESC;
# ASC(default) 오름차순, DESC 내림차순

sqlite> SELECT * FROM classmates ORDER BY age ASC name DESC;
```



### LIMIT

```sql
SELECT * FROM column LIMIT num

# 특정 table에서 원하는 개수만큼 가져오기
SELECT * FROM column LIMIT num OFFSET num;
```



### GROUP BY

특정 컬럼을 기준으로 그룹화 하기

```sql
SELECT column FROM table GROUP BY column;
```