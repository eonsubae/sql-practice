# 테이블 조인

RDBMS에서 중요한 것
* 테이블을 나눠 데이터를 중복저장하는 것을 피하기
* 그런데 테이블을 나눠 중복저장을 피하는 것만으로는 원하는 데이터를 조합해 가져올 수 없다
* 이 때 필요한 것이 테이블 간 관계를 맺어 join을 통해 가져오는 것이다

join
* join을 위해서는 두 테이블 간의 관계를 맺는데 사용할 컬럼이 필요하다
* 각 테이블의 연결고리 컬럼(하나씩)을 WHERE절에서 각종 조건 연산자를 사용해 SELECT문으로 가져오는 것으로 조인을 수행한다
* 예를 들어 동등 연산자인 =를 사용하면 연결고리 컬럼 값이 같은 데이터만 가져오게 된다
* 일반적으로 테이블 간 연결고리 칼럼은 이름과 데이터형을 일치시킨다
  - 컬럼의 이름은 다르게 하더라도 데이터형은 일치시켜야 한다

실습을 위한 테이블 생성
```SQL
CREATE TABLE emp_master
(
  emp_id     NUMBER        NOT NULL, --사원번호
  emp_name   VARCHAR2(100) NOT NULL, --사원명
  gender     VARCHAR2(10),           --성별
  age        NUMBER,                 --나이
  hire_date  DATE,                   --입사일자
  dept_id    NUMBER,                 --부서아이디
  address_id NUMBER,                 --주소아이디
  CONSTRAINT emp_master_pk PRIMARY KEY (emp_id)
);

CREATE TABLE dept_master (
  dept_id    NUMBER NOT NULL,         --부서아이디
  dept_name  VARCHAR2(50),            --부서 명
  use_yn     VARCHAR2(2) DEFAULT 'Y', --사용여부
  dept_desc  VARCHAR2(100),           --부서설명
  CONSTRAINT dept_master_pk PRIMARY KEY (dept_id)
);

CREATE TABLE address_master (
  address_id   NUMBER NOT NULL,       --주소아이디 
  city         VARCHAR2(100),         --도시 명 
  gu           VARCHAR2(50),          --구 명
  address_name VARCHAR2(100),         --나머지 주소
  CONSTRAINT address_master_pk PRIMARY KEY (address_id)
);
```
* 사원정보, 부서정보, 주소정보 테이블을 각각 생성한다
  
테스트를 위한 더미데이터 입력
```SQL
INSERT INTO emp_master ( emp_id, emp_name, gender, age, hire_date, dept_id, address_id )
VALUES (1, '김유신', '남성', 56, TO_DATE('2018-01-01', 'YYYY-MM-DD'), 1, 1);

INSERT INTO emp_master ( emp_id, emp_name, gender, age, hire_date, dept_id, address_id )
VALUES (2, '신사임당', '여성', 34, TO_DATE('2018-01-01', 'YYYY-MM-DD'), 1, 2);

INSERT INTO emp_master ( emp_id, emp_name, gender, age, hire_date, dept_id, address_id )
VALUES (3, '홍길동', '남성', 45, TO_DATE('2018-01-01', 'YYYY-MM-DD'), 3, 2);

INSERT INTO emp_master ( emp_id, emp_name, gender, age, hire_date, dept_id, address_id )
VALUES (4, '강감찬', '남성', 23, TO_DATE('2018-01-01', 'YYYY-MM-DD'), 2, 3);

INSERT INTO emp_master ( emp_id, emp_name, gender, age, hire_date, dept_id, address_id )
VALUES (5, '세종대왕', '남성', 45, TO_DATE('2018-01-01', 'YYYY-MM-DD'), 4, 4);


INSERT INTO dept_master ( dept_id, dept_name )
VALUES (1, '회계팀');

INSERT INTO dept_master ( dept_id, dept_name )
VALUES (2, '경영팀');

INSERT INTO dept_master ( dept_id, dept_name )
VALUES (3, '전산팀');

INSERT INTO dept_master ( dept_id, dept_name )
VALUES (4, '마케팅팀');


INSERT INTO address_master ( address_id, city, gu, address_name )
VALUES (1, '서울특별시', '중구', '새문안로 12');

INSERT INTO address_master ( address_id, city, gu, address_name )
VALUES (2, '서울특별시', '서대문구', '연희로 15길');

INSERT INTO address_master ( address_id, city, gu, address_name )
VALUES (3, '서울특별시', '영등포구', '여의대로 99');

INSERT INTO address_master ( address_id, city, gu, address_name )
VALUES (4, '서울특별시', '강남구', '테헤란로 33');

COMMIT;
```

내부 조인(Inner Join)
* 조인 중 가장 기본이 되는 조인이 내부 조인Inner Join이다
* 내부 조인은 WHERE절에서 동등 연산자인 =를 사용해 조인 컬럼(연결고리 컬럼)을 비교하는 조인이다

사원정보 테이블을 기준으로 부서정보를 조인하는 쿼리 작성하기
```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
    ,dept_master b
WHERE a.dept_id = b.dept_id
ORDER BY a.emp_id; 
```
* 사원정보 테이블에는 a라는 별칭을
* 부서정보 테이블에는 b라는 별칭을 사용하고 있다
* WHERE절에서 각 테이블의 조인 컬럼을 동등 연산자를 사용해 비교하고 있는 것을 확인할 수 있다

ANSI조인으로 내부조인 사용하기
* 위 쿼리처럼 WHERE절에서의 비교가 아닌 INNER JOIN이라는 키워드를 사용할 수도 있다

```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
INNER JOIN dept_master b
ON a.dept_id = b.dept_id
ORDER BY a.emp_id;
```
* ANSI조인은 INNER JOIN 키워드 다음에 조인할 테이블을 명시한다
* 그리고 WHERE절 대신 ON 키워드에서 조인컬럼들을 비교한다
* 물론 추가조건이 필요하다면 WHERE절을 작성해야 한다

내부 조인에서 남성만 조회
```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
    ,dept_master b
WHERE a.dept_id = b.dept_id
AND a.gender = '남성'
ORDER BY a.emp_id;
```
* 다른 조건을 추가하면서 WHERE절을 사용한 내부 조인을 사용할때는 AND로 조건을 연결해주면 된다

```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
INNER JOIN dept_master b
ON a.dept_id = b.dept_id
WHERE a.gender = '남성'
ORDER BY a.emp_id;
```
* ANSI조인에서는 WHERE절을 추가해주면 된다

3개의 테이블을 조인하기

```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn,
       c.address_id, c.city, c.gu, c.address_name
FROM emp_master a
    ,dept_master b
    ,address_master c
WHERE a.dept_id = b.dept_id
AND a.address_id = c.address_id
AND a.gender = '남성'
ORDER BY a.emp_id;
```
* 기본 구문에서는 FROM과 WHERE절에 추가로 테이블과 조인 컬럼을 명시하면 된다

```sql
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn,
       c.address_id, c.city, c.gu, c.address_name
FROM emp_master a
INNER JOIN dept_master b
ON a.dept_id = b.dept_id
INNER JOIN address_master c
ON a.address_id = c.address_id
WHERE a.gender = '남성'
ORDER BY a.emp_id;
```
* ANSI구문에서는 INNER JOIN, ON을 추가해 반복해서 작성하면 된다

---

외부 조인Outer Join
* 이름만 보면 내부 조인의 반대처럼 보일 수도 있으나 꼭 그런 것은 아니다
* 내부 조인이 두 테이블에서 같은 값을 조회한다면 외부 조인은 두 테이블 중 한 테이블의 조인 컬럼 값이 없는 건까지 모두 포함해 조회하는 조인이다

실습을 위한 추가 데이터 입력하기

```sql
INSERT INTO emp_master ( emp_id, emp_name, gender, age, hire_date, dept_id, address_id )
VALUES (6, '왕건', '남성', 35, TO_DATE('2018-01-01', 'YYYY-MM-DD'), NULL, 4);

COMMIT;
```
* 이름이 왕건인 사원 데이터는 부서 정보를 입력하지 않았다
* 따라서 내부 조인으로 부서정보와 함께 조회하면 왕건 데이터는 조회되지 않는다
* 이전처럼 조인을 하되 왕건처럼 부서정보가 없는 데이터까지 조회하고 싶다면 외부 조인을 사용하면 된다

외부조인을 사용해 부서정보가 없는 사원까지 조회하기
```sql
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
    ,dept_master b
WHERE a.dept_id = b.dept_id(+)
ORDER BY a.emp_id;
```
* 내부조인과 다른점은 기준 테이블(여기서는 사원정보 테이블)이 아닌 조인컬럼 뒤에 (+)를 붙여주는 것이다
* 그런데 (+)기호는 오라클에서만 사용할 수 있다
* 만약 다른 RDBMS에서도 사용하고 싶다면 ANSI 문법을 사용해야 한다

ANSI문법을 사용해 변경하기
```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
LEFT JOIN dept_master b
ON a.dept_id = b.dept_id
ORDER BY a.emp_id;
```
* LEFT JOIN이란 구문의 왼쪽 테이블을 기준 테이블로 삼는 다는 것이다

부서정보 테이블을 기준 테이블로 사용해보기
* 우선 실습을 위한 더미 데이터를 추가한다

```SQL
INSERT INTO dept_master ( dept_id, dept_name )
VALUES (5, 'IT팀');

COMMIT;
```
* 현재까지 입력했던 사원 정보 테이블에 부서정보가 5인 사원은 없었다
* 그럼에도 IT팀까지 모든 부서정보를 조회하고자 할 때는 다음처럼 쿼리를 작성한다

오라클 기본 외부조인 구문
```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
    ,dept_master b
WHERE a.dept_id(+) = b.dept_id
ORDER BY a.emp_id;
```

ANSI 외부조인 구문
```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
RIGHT JOIN dept_master b
ON a.dept_id = b.dept_id
ORDER BY a.emp_id;
```

두 테이블의 데이터 모두를 조회하는 방법
* 각 테이블의 없는 정보를 조회하기 위해 조인 컬럼 뒤에 (+)을 여러개 붙이면 에러가 난다
* 따라서 이 경우에는 ANSI 문법을 사용한 외부 조인을 할 수 밖에 없다

ANSI 문법으로 작성한 FULL OUTER JOIN
```SQL
SELECT a.emp_id, a.emp_name, a.gender, a.age, a.dept_id,
       b.dept_id, b.dept_name, b.use_yn
FROM emp_master a
FULL OUTER JOIN dept_master b
ON a.dept_id = b.dept_id
ORDER BY a.emp_id;
```