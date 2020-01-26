# SQL입문

## SQL의 종류
* 여기서 SQL의 종류란 SQL이 처리하는 문장의 성격을 말한다
* SQL은 크게는 두 가지, 세분하면 네 가지 유형으로 나눌 수 있다

DDL(Data Definition Language)
* RDBMS에는 테이블 외에도 뷰, 인덱스, 시퀀스 등 여러 데이터베이스 객체가 있다
* 이 객체들을 생성,삭제,수정 하는데 사용하는 SQL을 DDL이라고 한다
* CREATE: 객체 생성
* DROP: 객체 삭제
* ALTER: 객체 변경
* TRUNCATE TALBE: 테이블에 있는 모든 데이터 삭제
* RENAME: 객체 이름 변경

DML(Data Manipulation Language)
* DML은 가장 많이 사용하는 SQL문이다
* SELECT: 테이블이나 뷰에서 데이터를 조회
* INSERT: 데이터를 입력
* UPDATE: 기존에 저장된 데이터를 수정
* DELETE: 테이블에 있는 데이터를 삭제
* MERGE: 조건에 따라 INSERT와 UPDATE를 수행

TCL(Transaction Control Language)
* TCL은 DML로 인해 일어나는 변경을 한 번 더 체크해 실수를 방지하는 기능을 수행한다
* COMMIT: DML로 변경된 데이터를 DB에 적용
* ROLLBACK: DML로 변경된 데이터를 이전 상태로 되돌림

DCL(Data Control Language)
* DCL은 객체에 대한 권한을 할당하거나 회수하는 SQL문을 말한다
* GRANT: 객체에 대한 권한을 할당
* REVOKE: 객체에 할당된 권한을 회수

---

## 테이블 생성

테이블 생성 구문
* 테이블은 CREATE TABLE구문으로 생성한다. 예제는 다음과 같다
```sql
CREATE TABLE table_name (
  column_name1 datatype [NOT] NULL,
  column_name2 datatype [NOT] NULL,
  ...
  PRIMARY KEY ( column_list )
);
```
* 오라클에서 사용할 수 있는 테이블 이름과 컬럼이름에는 제약 사항이 있다
  - 30byte를 초과할 수 없음
  - 언더스코어(_), 문자, 숫자를 사용할 수 있지만, 첫 문자는 반드시 문자로 시작해야 한다

컬럼의 데이터형
* 데이터베이스의 데이터형은 크게 문자, 숫자, 날짜 데이터로 나눌 수 있다
* 오라클을 포함한 RDBMS는 이 세종류의 데이터형을 좀 더 세분화했다
* 오라클에서 제공하는 대표적인 데이터형은 다음과 같다
* 물론 아래의 데이터 형보다 더 많은 것들이 있지만 이것들만 사용해도 웬만한 데이터를 모두 다룰 수 있다 

| 데이터 유형 | 데이터형               | 설명                                                          |
| ------ | ------------------ | ----------------------------------------------------------- |
| 문자형    | CHAR(n)            | 고정 길이 문자, 최대 2000byte                                       |
| 문자형    | VARCHAR2(n)        | 가변 길이 문자, 최대 4000byte                                       |
| 숫자형    | NUMBER[ (p, [s]) ] | p(1~38, 디폴트 값은 38)와 s(-84~127, 디폴트 값은 0)는 십진수 기준, 최대 22byte |
| 날짜형    | DATE               | BC 4712년 1월 1일부터 9999년 12월 31일까지 년,월,일,시,분,초까지 입력 가능        |

괄호(n)의 의미
* 예를 들어 VARCHAR2(10)은 VARCHAR2형 문자를 10byte사용한다는 의미다
  - 즉 데이터를 10byte까지 해당 컬럼에 입력할 수 있게 된다. 데이터 크기가 이를 넘어가면 오류가 발생한다

CHAR와 VARCHAR2의 차이점
* CHAR는 고정 길이, VARCHAR2는 가변 길이의 문자형 데이터를 각각 저장한다
* 예를 들어 CHAR(10)형으로 컬럼을 만들고 'A'란 문자를 넣으면 데이터 크기는 1byte가 아닌 10byte가 된다
  - 반대로 VARCHAR2(10)형으로 만들었다면 1byte만 차지한다
  - 이처럼 VARCHAR2형을 사용하면 실제 데이터에 따라 크기가 정해지므로 특별한 경우가 아니면 문자형 컬럼은 VARCHAR2형으로 만드는 것이 좋다

NUMBER형
* 괄호의 p값은 자리수 크기를 의미한다
* 괄호의 p값을 지정하지 않으면 오라클은 기본적으로 38자리 수까지 나타낼 수 있다
* s값은 소수점 이하 유효숫자 자리 수를 의미한다
* s값을 생략하면 기본 값은 0이된다

DATE형
* 오라클의 DATE형은 년-월-일, 시-분-초 단위까지 저장할 수 있다
* 더 세밀하게 날짜를 저장하려면 TIMESTAMP형을 사용하면 된다

NULL
* 데이터가 없음을 의미한다
* 해당 컬럼에 값이 들어가지 않을 수 있다고 정의하기 위해 사용한다
  - 반면 NOT NULL은 해당 컬럼에는 반드시 값이 들어가야 한다고 정의하기 위해 사용한다
* NULL이나 NOT NULL중 아무것도 명시하지 않으면 오라클은 기본적으로 NULL이 들어가도록 지정한다

기본 키(Primary Key)
* 테이블에서 유일한 값을 식별하는 역할을 한다
  - 따라서 반드시 NOT NULL을 명시해야 한다
* 기본 키 값에 중복된 값을 입력하면 오라클은 오류를 반환하며 해당 작업은 취소된다
  - 그리고 오라클은 기본 키를 생성하면 자동으로 해당 키 컬럼에 유니크 인덱스를 만들어 준다
* 테이블 당 1개만 만들 수 있다
* 컬럼 1개로 만들 수도, 여러 컬럼을 결합해 만들 수도 있다
* 컬럼이 1개뿐인 경우 아래와 같이 PRIMARY KEY 구문을 넣어 생성할 수 있다

```SQL
Column_name1 VARCHAR2(10) NOT NULL PRIMARY KEY,
...
```

* 컬럼이 2개 이상이면 아래와 같이 명시한다


```SQL
PRIMARY KEY ( column1, column2, ... )
```

직접 테이블 생성해보기
* 테이블은 다음과 같은 모습이어야 한다
  - 우선 모습을 보고 실제 SQL문을 어떻게 작성해야 할지 생각해보자

| 컬럼 명      | 컬럼 설명 | 데이터 형         | NULL     | 기본 키 |
| --------- | ----- | ------------- | -------- | ---- |
| emp_id    | 사원 번호 | NUMBER        | NOT NULL | Y    |
| emp_name  | 사원 명  | VARCHAR2(100) | NOT NULL |      |
| gender    | 성별    | VARCHAR2(10)  | NULL     |      |
| age       | 나이    | NUMBER        | NULL     |      |
| hire_date | 입사일자  | DATE          | NULL     |      |
| etc       | 기타    | VARCHAR2(300) | NULL     |      |

```SQL
CREATE TABLE emp_table (
  emp_id NUMBER NOT NULL,
  emp_name VARCHAR2(100) NOT NULL,
  gender VARCHAR2(10) NULL,
  age NUMBER NULL,
  hire_date DATE NULL,
  etc VARCHAR2(300) NULL,
  PRIMARY KEY ( emp_id )
);
```

테이블이 잘 생성됐는지 확인해보기
```sql
SELECT * FROM emp_table;
```
* 물론 아직 입력한 데이터가 없기 때문에 아무런 데이터도 들어있지 않을 것이다