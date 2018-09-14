# Section 3. Creating Database and Tables

### lecture 24. Creating Databases

네 개의 데이터베이스 서버 만들기
* Dog Walker
* Soap Shop
* Practice
* News Site

show databases;
* 현존하는 데이터베이스 목록을 불러온다

CREATE DATABASE 이름;
* 지정한 이름의 데이터베이스를 만든다
* CREATE DATABASE hello_world_db;
  - hello_world_db라는 데이터 베이스를 만든다
  - 그런 다음 다시 show databases;를 입력해서 확인해보자
* 이 강의에서는 스네이크 케이스를 컨벤션으로 한다
* 명령어는 대소문자 상관없이 사용해도 된다

---

### lecture 25. CODE: Creating Databases

데이터 베이스 생성 코드 정리

Start the CLI:
* mysql-ctl cli; 

List available databases:
* show databases; 

The general command for creating a database:
* CREATE DATABASE database_name; 

A specific example:
* CREATE DATABASE soap_store; 

---

### lecture 26. Dropping Databases

데이터 베이스 제거하기

DROP DATABASE 이름;
* 지정한 이름의 데이터베이스를 삭제한다

---

### lecture 28. Using Databases

USE 이름;
* 지정한 이름의 데이터베이스를 사용한다

SELECT database();
* 현재 사용중인 데이터베이스를 알려준다
* drop으로 현재 사용중인 데이터베이스를 삭제하고 다시 명령어를 입력하면 null값이 출력된다

---

### lecture 30. Introduce to Tables

데이터베이스는 테이블들의 묶음이다
* 적어도 관계형 DB에서는 맞는 말이다

위키피디아가 정의한 테이블
* 데이터베이스에서 구조화된 형식을 사용해서 관계지어진 데이터의 집합

예시) Cats
* 컬럼은 name, breed, age만 사용한다
* 로우에는 실제 데이터들을 저장한다

| Name | Breed | Age |
|------|-------|-----|
| Blue | Scottish Fold | 1 |
| Rocket | Persian | 3 |
| Monty | Tabby | 10 |
| Sam | Munchkin | 5 |

---

### lecture 31. The Basic Datatypes

데이터 타입이 왜 중요한지 살펴보기

예시
| Name | Breed | Age |
|------|-------|-----|
| Blue | Scottish Fold | 1 |
| Rocket | Persian | 3 |
| Monty | Tabby | ten |
| Sam | Munchkin | I am yung cat |

Age를 계산하려 한다면?
* ex. Age * 7을 하면 어떻게 될까?
  - Monty와 Sam은 계산할 수 없다
* 이렇게 의도치 않은 결과를 얻지 않으려면 테이블을 만들때 정확한 데이터 타입을 사용해야한다

정확한 데이터 타입
* 위와 같은 테이블을 작성하려면 name과 breed는 문자열
* 그리고 age는 숫자형으로 지정해야 한다
* 그런데 실제 mysql을 포함한 여러 데이터베이스에서는 수많은 서로 다른 데이터 타입들을 가지고 있다
  - 하지만 많은 타입들을 전부 사용할 필요는 없다

많이 사용되는 데이터 타입
* 문자열 타입
  - VARCHAR
* 숫자 타입
  - INT

INT
* 정수
* -2,147,483,648 ~ 2,147,483,647가 INT의 범위다

VARCHAR
* CHAR는 해당 컬럼의 데이터들은 전부 같은 길이의 문자열만을 입력해야 한다
* VARCHAR는 다양한 길이의 문자열 입력이 가능
  - 1 ~ 255
* VARCHAR를 사용할때는 괄호에 최대 길이를 지정해야 한다
* ex. VARCHAR(100)을 지정하면 최대 100자 까지만 저장된다
  - 이 이상의 길이를 입력하면 초과하는 문자열은 버리고 100자 까지만 저장한다

---

### lecture 33. Basic Datatypes Challenge

트위터 테이블 만들어 보기
* 컬럼은 최소 3개가 있어야 한다
  - username(max 15 chars)
  - The tweet content(max 140 chars)
  - Number of favorites
* 각 컬럼의 데이터 타입 지정하기
  - username은 varchar(15)
  - content는 varchar(140)
  - favorites는 int

예시
| username | content | favorites |
|----------|---------|-----------|
| 'coolguy' | 'my first tweet!' | 1 |
| 'guitar_queen' | 'I love music:)' | 10 |
| 'lonely_heart' | 'still looking 4 love' | 0 |

---

### lecture 35. Creating Your Own Tables

테이블 만들기 명령어 
```sql
CREATE TABLE 테이블이름
  (
    컬럼명 데이터 타입,
    컬럼명 데이터 타입,
  );
```

---

### lecture 37. How Do We Know It Worked?

테이블 목록 보기
```sql
SHOW TABLES;
```

특정 테이블의 컬럼 목록 보기
```sql
SHOW COLUMNS FROM 테이블명;
```
* DESC 테이블명; 으로도 사용할 수 있다

---

### lecture 39. Dropping Tables

테이블 삭제하기
```sql
DROP TABLE 테이블명;
```

---

### lecture 41. Creating Your Own Tables Challenge

pastries 테이블 만들기
* 2개의 컬럼이 있어야 한다
  - name(max char 50)
  - quantity(number)
* 테이블과 컬럼을 만들고 CLI로 검사해보기
* 검사가 완료되면 만든 테이블을 삭제하기

내 풀이
```sql
create table pastries (
  name VARCHAR(50),
  quantity INT
);

show tables;

show columns from pastries;

desc pastries;

drop table pastries;
```