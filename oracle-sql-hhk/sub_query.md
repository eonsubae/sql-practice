# 서브쿼리Subquery

서브쿼리란
* 서브쿼리는 메인쿼리 안에 있는 보조, 하위 쿼리를 의미한다
* 서브쿼리를 구성하는 SELECT 문은 항상 괄호로 둘러싸여 있다

서브쿼리를 사용하는 대표적인 경우
* SELECT 절에서 사용되어 특정 값을 반환하는 경우가 있다
* FROM 절에서 사용되어 마치 테이블처럼 사용할 수도 있다
* WHERE 절에서 조건 값을 비교하는 데도 사용된다

스칼라 서브쿼리scalar subquery
* 메인 쿼리의 SELECT 절에서 마치 컬럼이나 표현식처럼 사용된다
* 하나의 컬럼이나 표현식 역할을 하므로 두 개 이상의 값(두 개 이상의 컬럼)을 반환하면 안된다
* 또한 반환되는 행의 수도 1개여야 한다

스칼라 서브쿼리 작성해보기
```SQL
SELECT a.emp_id
      ,a.emp_name
      ,a.gender
      ,a.age
      ,a.dept_id
      ,( SELECT b.dept_name
           FROM dept_master b
          WHERE a.dept_id = b.dept_id ) dept_name
FROM emp_master a;
```
* 사원정보 테이블에는 부서의 ID값만 있고 이름이 없으므로 서브쿼리로 가져오고 있다

인라인 뷰inline view
* 메인 쿼리의 FROM 절에서 사용하는 서브쿼리를 말한다
* FROM절에서 사용되므로 서브쿼리 자체가 하나의 테이블처럼 사용된다
* 스칼라 서브쿼리와 달리 여러 개의 컬럼, 표현식, 로우의 반환이 가능하다

```SQL
SELECT a.dept_id
      ,a.dept_name
      ,k.emp_id
      ,k.emp_name
      ,k.address 
  FROM dept_master a
      ,( SELECT b.emp_id
               ,b.emp_name
               ,c.city || c.gu || c.address_name AS address
               ,b.dept_id
           FROM emp_master b
               ,address_master c
          WHERE b.address_id = c.address_id
       ) k
 WHERE a.use_yn = 'Y'
   AND a.dept_id = k.dept_id
 ORDER BY 1, 3;
```

중첩 서브쿼리Nested subquery
* WHERE절에서 사용하는 서브쿼리다
* WHERE절에서 사용되므로 조건절의 일부로 사용된다
* 즉, 메인쿼리 테이블의 특정 컬럼 값과 비교한 값을 반환하는 용도로 사용된다
* 인라인 뷰처럼 여러 개의 컬럼, 로우를 반환할 수 있다

```SQL
SELECT *
  FROM dept_master a      
 WHERE a.dept_id = ( SELECT b.dept_id
                       FROM emp_master b
                      WHERE b.emp_name = '세종대왕'
				    )
;
```

세미조인과 안티조인
* 세미조인과 안티조인은 서브쿼리와 같이 사용하는 조인이다

세미조인
* 메인쿼리에서 사용된 테이블과 서브쿼리 결과를 조인하는 것을 말한다
* 동등 연산자나 IN 연산자를 많이 사용한다. 경우에 따라 EXISTS 연산자를 사용하기도 한다

```SQL
SELECT *
  FROM dept_master a      
 WHERE EXISTS ( SELECT 1
                  FROM emp_master b
                 WHERE b.age BETWEEN 40 AND 49
                   AND a.dept_id = b.dept_id
              );
```
* EXISTS 연산자는 메인쿼리의 테이블 데이터를 기준으로 서브쿼리에서 반환하는 데이터가 존재하면 TRUE을 반환한다
* 특이한 점은 메인쿼리와 서브쿼리의 조인 조건절을 서브쿼리 내의 WHERE절에서 기술한다는 것이다

안티조인
* 세미조인과 형식은 같으나 NOT 연산자가 들어간다는 점이 다르다
* NOT에 반대라는 의미가 있어 안티조인이라는 명칭이 붙었다

```SQL
SELECT *
  FROM dept_master a      
 WHERE NOT EXISTS ( SELECT 1
                      FROM emp_master b
                     WHERE b.age BETWEEN 40 AND 49
                       AND a.dept_id = b.dept_id
                   );	
```
