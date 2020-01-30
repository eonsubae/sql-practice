# 집합 쿼리

수학에서 배웠던 집합 개념을 이용해서 원하는 데이터만 추출하기

- 우선 아래처럼 테이블을 생성하고 더미 데이터를 입력한다

```SQL
CREATE TABLE employee
(
  emp_id2    NUMBER        NOT NULL,
  emp_name2  VARCHAR2(100) NOT NULL,
  gender     VARCHAR2(10),
  age        NUMBER,
  hire_date  DATE,
  etc        VARCHAR2(300),
  CONSTRAINT employee_pk PRIMARY KEY (emp_id2)
);

INSERT INTO employee ( emp_id2, emp_name2, gender, age, hire_date )
VALUES (1, '선덕여왕', '여성', 23, TO_DATE('2018-02-01', 'YYYY-MM-DD'));

INSERT INTO employee ( emp_id2, emp_name2, gender, age, hire_date )
VALUES (2, '허난설헌', '여성', 33, TO_DATE('2018-02-01', 'YYYY-MM-DD'));

INSERT INTO employee ( emp_id2, emp_name2, gender, age, hire_date )
VALUES (3, '김만덕', '여성', 43, TO_DATE('2018-02-01', 'YYYY-MM-DD'));

INSERT INTO employee ( emp_id2, emp_name2, gender, age, hire_date )
VALUES (4, '장희빈', '여성', 35, TO_DATE('2018-02-01', 'YYYY-MM-DD'));

INSERT INTO employee ( emp_id2, emp_name2, gender, age, hire_date )
VALUES (5, '신사임당', '여성', 45, TO_DATE('2018-02-01', 'YYYY-MM-DD'));

COMMIT;
```

UNION ALL

- 두 집합의 모든 원소를 가져오는 합집합 개념과 비슷하다(같지는 않다)
- 두 개의 SELECT문을 UNION ALL로 연결하면 각 문장이 반환하는 결과가 모두 조회된다

UNION ALL의 일반적인 구문

```SQL
SELECT col1, col2, ...
FROM ...
WHERE ...
UNION ALL
SELECT col1, col2, ...
FROM ...
WHERE ...
```

- 기존 SELECT문과 다른 점은 두 개의 SELECT문 사이에 집합 쿼리인 UNION ALL이 들어갔다는 것 뿐이다
- 다른 집합 쿼리도 사용방법은 동일하다

이전 insert_and_delete 장에서 만들었던 emp_table과 employee테이블을 합치기

```sql
SELECT emp_id, emp_name, gender, age
  FROM emp_table
UNION ALL
SELECT emp_id2, emp_name2, gender, age
  FROM employee;
```

- 두 테이블에 있는 모든 데이터가 조회될 것이다
- 이 때 주의해야할 점은 두 SELECT절에서 사용하는 컬럼의 데이터형과 수가 일치해야 한다는 것이다

집합 연산자를 사용한 데이터를 정렬하기

- 일반 SELECT문과 동일하게 ORDER BY를 쓰면된다
- ORDER BY는 문장 맨 끝에만 쓸 수 있으므로 두 번째 SELECT문 끝에서만 사용할 수 있다
- 그런데 정렬할 컬럼명을 기술할 때는 첫 번째 SELECT문의 컬럼명을 사용해야 한다

UNION

- UNION ALL과 달리 중복된 데이터가 있어도 1건만 조회한다
- 따라서 수학적 합집합 개념과 같은 집합 쿼리는 UNION ALL이 아닌 UNION이라 할 수 있다

중복 데이터에 대한 UNION ALL 사용

```SQL
SELECT emp_name
  FROM emp_table
 UNION ALL
SELECT emp_name2
  FROM employee
 ORDER BY 1;
```

- 각 테이블에 있는 신사임당 데이터가 중복되어 나올 것이다

중복 데이터에 대한 UNION 사용

```SQL
SELECT emp_name
  FROM emp_table
 UNION
SELECT emp_name2
  FROM employee
 ORDER BY 1;
```

- 신사임당 데이터는 1번만 조회될 것이다

값이 다른 컬럼이 있는 경우

- 위 쿼리의 경우 이름만을 가지고 데이터를 비교했다
- 여러 개의 컬럼을 가지고 비교한다면 컬럼값이 하나라도 다른 경우 UNION을 써도 중복되지 않은 데이터로 인식한다

```sql
SELECT emp_name, gender, age
  FROM emp_table
 UNION
SELECT emp_name2, gender, age
  FROM employee
 ORDER BY 1;
```

- 다시 신사임당이 2번 조회될 것이다

INTERSECT

- 두 집합의 공통 원소만 추출하는 교집합과 비슷한 집합 쿼리
- UNION은 중복된 데이터를 한 건만 추출한다면, INTERSECT는 오직 중복 데이터만 추출한다

```SQL
SELECT emp_name
  FROM emp_table
INTERSECT
SELECT emp_name2
  FROM employee
 ORDER BY 1;
```

- 이름이 같은 신사임당 데이터만 조회될 것이다

```sql
SELECT emp_name, gender, age
  FROM emp_table
INTERSECT
SELECT emp_name2, gender, age
  FROM employee
ORDER BY 1;
```

- 여러 컬럼을 비교하면 중복된 데이터가 없으므로 선택된 행이 없을 것이다

MINUS

- 한 집합을 기준으로 다른 집합에 없는 요소만 추출하는 차집합과 비슷한 집합 쿼리다

```SQL
SELECT emp_name
  FROM emp_table
MINUS
SELECT emp_name2
  FROM employee
 ORDER BY 1;
```

- 이름을 기준으로 비교했으므로 emp_table도 가진 신사임당을 제외한 employee의 모든 데이터가 추출된다

```sql
SELECT emp_name2
  FROM employee
MINUS
SELECT emp_name
  FROM emp_table
 ORDER BY 1;
```

- 테이블 순서를 바꾼대로 emp_table에서 신사임당을 제외한 모든 데이터가 추출된다
