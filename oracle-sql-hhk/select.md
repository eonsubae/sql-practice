# 데이터 조회, SELECT문

SELECT문
* SELECT문은 DML의 가장 기본이 되는 문이다
* 기본적으로 데이터 조회에 사용되지만 INSERT, UPDATE, DELETE문과 결합되어 사용되기도 한다

SELECT문의 기본 구문
```SQL
SELECT column1, column2, ...
FROM 테이블 명
WHERE 조건
ORDER BY 정렬 순서;
```
* SELECT문은 SELECT, FROM, WHERE, ORDER BY의 네 부분(절)으로 나눌 수 있다
* 이 네 부분(절)을 각각 알아보자

SELECT절
* 테이블에서 선택할 컬럼과 표현식을 명시한다
  - 명시한 순서대로 데이터를 가져온다
  - 표현식이란 하나 이상의 값, 연산자 SQL함수가 결합된 식을 말한다. 이후에 알아볼 것이다
* *를 입력하면 전체 컬럼을 선택한다

FROM절
* 조회하고자 하는 테이블을 명시한다
* 보통은 하나의 테이블만 조회하지만 여러 테이블을 결합하기도 한다
  - 이 때는 조회할 여러 테이블을 콤마로 구분해 FROM절에 명시한다

WHERE절
* 특정 조건에 맞는 데이터를 가져올 때 사용한다
* WHERE절을 생략하면 해당 테이블의 모든 데이터를 조회한다

ORDER BY절
* 조회한 데이터를 정렬해서 보여주는 역할을 한다
* 생략하면 무작위로 데이터가 조회된다
* 정렬은 기본적으로 오름차순, 내림차순이 있다
  - 오름차순은 정렬하려는 컬럼 끝에 ASC를,
  - 내림차순은 정렬하려는 컬럼 끝에 DESC를 입력하면 된다
  - ASC는 기본값이므로 생략해도 된다

특정 칼럼만 조회하기
* 앞서 subway_statistics 테이블의 특정 칼럼만 조회해보자

```sql
SELECT seq_id
      ,station_name
      ,boarding_time
      ,passenger_number
FROM subway_statistics;
```

조건에 맞는 데이터 조회하기
* 데이터 중 특정 조건에 맞는 데이터를 조회하려면 WHERE절을 사용해야 한다
* 예를 들어, 잠실역의 데이터만 골라내보자

```SQL
SELECT * 
FROM subway_statistics
WHERE station_name = '잠실(216)';
```

조건 연산자

| 조건 연산자 | 기능                       |
| ------ | ------------------------ |
| =      | 두 값이 같을 때 참              |
| !=,<>  | 두 값이 다를 때 참              |
| >      | 왼쪽 값이 오른쪽 값보다 클 때 참      |
| <      | 왼쪽 값이 오른쪽 값보다 작을 때 참     |
| >=     | 왼쪽 값이 오른쪽 값보다 크거나 같을 때 참 |
| <=     | 왼쪽 값이 오른쪽 값보다 작거나 같을 때 참 |
* 연산자들은 문자형이나 숫자형 모두 사용할 수 있다
* 단, 문자는 문자끼리 숫자는 숫자끼리 연산해야 한다

연산자 사용 예시
* 지하철역이 잠실(216)이 아닌 경우
```SQL
WHERE
  station_name != '잠실(216)'

  혹은

WHERE
  station_name <> '잠실(216)'  
```

* 승차 인원만 검색
```sql
WHERE gubun = '승차'
```

* 8시 이후 승하차한 건 전체 찾기
```sql
WHERE boarding_time >= 8
```

* 승하차 인원이 500명 초과인 건 찾기
```sql
WHERE passenger_number > 500
```

여러 조건으로 검색하기
* 조건 모두를 만족해야 하는 경우 AND를 하나만 만족 해도 되는 경우 OR을 사용한다

* 잠실역에서 7시에 승차한 건을 조회
```sql
WHERE
  station_name = '잠실(216)'
  AND gubun = '승차'
  AND boarding_time = 7
```

* 선릉역에서 9시에 승차한 인원을 조회
```sql
WHERE
  station_name = '선릉(220)'
  AND gubun = '승차'
  AND boarding_time = 9
```

* 선릉역이나 잠실역에서 승하차한 건을 조회
```SQL
WHERE station_name = '잠실(216)'
   OR station_name = '선릉(220)'
```

지금까지 공부한 것들을 종합해서 잠실역에서 7시나 9시에 승하차한 건을 조회해보기

```sql
SELECT *
FROM subway_statistics
WHERE station_name = '잠실(216)'
  AND ( boarding_time = 7
   OR   boarding_time = 9 );
```
* boarding_time이 복수인 경우를 표현하려면 괄호를 사용해줘야 제대로 작동한다
* 만약 위 쿼리문에서 괄호가 없으면 9시에 승하차한 모든 건이 조회된다

잠실(216)이 아닌 잠실만으로 역명 조회하기
* 역 숫자 코드를 쿼리문을 작성할때마다 찾는 것은 비효율적이다
* LIKE 연산자를 이용하면 이 문제를 해결할 수 있다

선릉역을 조회하기
```sql
WHERE station_name LIKE '선릉%'
```
* %는 모든 것을 의미한다
* 위 WHERE절은 선릉으로 시작되는 모든 건을 조회한다는 의미다
* 만약 '선%'였다면 선으로 시작되는 모든 건이 조회될 것이다
* 반대로 '%선'이라면 선으로 끝나는 모든 건이 조회될 것이다

잠실로 시작되는 모든 데이터 조회하기
```SQL
SELECT *
FROM subway_statistics
WHERE station_name LIKE '잠실%';
```

잠실역만 조회하기
```sql
SELECT *
FROM subway_statistics
WHERE station_name LIKE '잠실(%';
```

IN 연산자
* 앞서 7시와 9시 승하차건을 조회했을 때 OR을 사용했다
* 그런데 OR은 이런 상황 외에도 보다 폭넓게 활용되므로 모든 건을 OR을 사용해서 처리하면 혼동되는 경우가 있다
* IN을 사용하면 특정 컬럼의 여러 값을 보다 시맨틱하게 처리할 수 있다

IN 연산자를 사용해 선릉역에서 7시 또는 9시에 승하차 건을 조회하기
```SQL
SELECT *
FROM subway_statistics
WHERE station_name LIKE '선릉%'
  AND boarding_time IN (7, 9);
```

BETWEEN 연산자
* 컬럼 값을 비교할 때 >, >=, <, <=연산자를 사용한다는 것을 배웠다
* 예를 들어 선릉역의 승하차 인원이 500명 이상 1000명 이하인 건을 조회하는 쿼리문은 다음과 같다

```SQL
SELECT *
FROM subway_statistics
WHERE station_name LIKE '선릉%'
  AND passenger_number >= 500
  AND passenger_number <= 1000;
```
* 위 쿼리문을 BETWEEN 연산자를 사용해 처리하면 OR과 IN의 때와 마찬가지로 보다 시맨틱하게 처리할 수 있다

```SQL
SELECT *
FROM subway_statistics
WHERE station_name LIKE '선릉%'
  AND passenger_number BETWEEN 500 AND 1000;
```

데이터 정렬하기 - ORDER BY
* ORDER BY를 사용하면 기본적으로 데이터를 오름차순 또는 내림차순으로 정렬해 보다 편리하게 볼 수 있다
* 이 외에도 좀 더 다양하게 ORDER BY를 사용하는 방법도 있다

| 제목                           | 내용                                        |
| ---------------------------- | ----------------------------------------- |
| ORDER BY col1, col2          | col1 컬럼 오름차순 후, col2 컬럼 오름차순 정렬           |
| ORDER BY col1 DESC, col2     | col1 컬럼 내림차순 후, col2 컬럼 오름차순 정렬           |
| ORDER BY col2 ASC, col1 DESC | col2 컬럼 오름차순 후, col1 컬럼 내림차순 정렬           |
| ORDER BY 1, 2                | SELECT 절에 명시한 첫 번째와 두 번째 컬럼을 순서대로 오름차순 정렬 |

지하철역명 순으로 정렬하기
```SQL
SELECT *
FROM subway_statistics
ORDER BY station_name;
```