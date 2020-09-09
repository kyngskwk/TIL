# :card_file_box: DATABASE

데이터베이스는 여러 사람이 공유하여 사용할 목적으로 체계화해 통합, 관리하는 데이터의 집합이다.



### DBMS

데이터베이스(DB)를 관리하는(Management) 시스템(System)

계층형 데이터베이스, 관계형 데이터베이스, 객체지향 데이터베이스가 존재



### RDBMS

관계형 모델을 기반으로하는 데이터베이스 관리시스템

<img width="1457" alt="_2020-09-05__6 19 41" src="https://user-images.githubusercontent.com/60081217/92621848-52ebf880-f2ff-11ea-97d0-95f711e6742d.png">



### 관계형 데이터베이스

- 관계형 데이터베이스는 관계를 열과 행으로 이루어진 테이블 집합으로 구성
- 각 열을 특정 종류의 데이터를 기록
- 테이블의 행은 각 객체/엔터티와 관련된 값의 모음

<img width="1172" alt="_2020-09-05__6 21 21" src="https://user-images.githubusercontent.com/60081217/92621852-53848f00-f2ff-11ea-8647-10dadf32f0a1.png">
<img width="1311" alt="_2020-09-05__6 22 28" src="https://user-images.githubusercontent.com/60081217/92621853-541d2580-f2ff-11ea-9d51-8fb042324dd1.png">

- 스키마 : 데이터베이스에서 자료의 구조와 제약 조건(구조, 표현 방법, 관계 등)에 관한 전반적인 명세 (타입이 지정된다.)

  <img width="875" alt="_2020-09-05__6 23 20" src="https://user-images.githubusercontent.com/60081217/92621857-554e5280-f2ff-11ea-84bd-24c36c7651eb.png">

- 테이블(관계) : 열과 행의 모델을 사용해 조작된 데이터 요소들의 집합

- Column(열), 속성 : 각 열 에는 고유한 데이터 형식이 있다.

- row(행), 레코드 : 테이블의 데이터는 행으로 저장된다. 테이블에 4명의 고객 정보가 저장되어 있으며 행은 4개가 존재한다.

- PK(Primary Key / 기본키) : 각 행의 고유값으로 저장된 레고드를 고유하게 식별할 수 있는 값



### SQL

기본적으로 데이터베이스를 관리하기 위해서 던지는 언어

관계형 데이터베이스 관리시스템(RDBMS)의 데이터를 관리하기 위해 설계된 특수 목적의 프로그래밍 언어

RDBMS에서 자료의 검색과 관리, 데이터베이스 스키마 생성과 수정, 데이터베이스 접근 관리 등을 위해 고안 됨

<img width="960" alt="_2020-09-05__6 28 45" src="https://user-images.githubusercontent.com/60081217/92621862-55e6e900-f2ff-11ea-8691-fa20d054d10a.png">
<img width="492" alt="_2020-09-05__9 25 17" src="https://user-images.githubusercontent.com/60081217/92621868-57181600-f2ff-11ea-887e-b7b2eaccf731.png">

- sqlite3 <filename> 생성 / 접근
- .tables 테이블 목록 조회
- .schema <table> 특정 테이블 스키마 조회