# 데이터 집계

데이터 분석의 기초

- 데이터 분석을 위해서는 단순히 테이블에 있는 데이터를 조사하는 것에서 그치면 안된다
- 데이터 분석의 가장 기본적인 단계는 테이블의 데이터들을 특정 컬럼 기준으로 집계하는 것이다
- 이를 위해서는 SELECT문에 GROUP BY절을 추가해야 한다

GROUP BY절

- GROUP BY절의 기본적인 구문

```SQL
SELECT expr1, expr2, ...
FROM ...
WHERE ...
GROUP BY expr1, expr2, ...
ORDER BY ...
```

GROUP BY절의 사용법

- WHERE절과 ORDER BY절 사이에 위치해 집계할 대상 칼럼이나 표현식을 명시하면 된다
  - 물론 WHERE과 ORDER BY는 생략도 가능하다
- GROUP BY절에 명시한 모든 컬럼 혹은 표현식은 SELECT절에도 명시해야 한다

GROUP BY절 예시

```SQL
SELECT station_name
FROM subway_statistics
WHERE gubun='승차'
GROUP BY station_name
ORDER BY station_name;
```

- 지하철 역 명으로 집계되어 gubun이 승차인 지하철역명이 단 한 번씩만 조회된다
- 그러나 아직은 데이터 분석을 하기에는 모자란 측면이 있다
- 데이터 분석은 특정 컬럼의 최댓값, 최솟값, 평균 등을 구하는 집계 함수와 같이 사용할 때 가능하다

집계 쿼리는 아니지만 GROUP BY절을 사용하지 않고 위 쿼리문과 같은 결과를 추출하는 방법

- DISTINCT키워드를 사용하기

```SQL
SELECT DISTINCT station_name
FROM subway_statistics
WHERE gubun = '승차'
ORDER BY 1;
```

- DISTINCT는 중복 값을 제외시켜 유일한 값들만 조회하므로 GROUP BY절을 사용한 효과를 낸다

주요 집계 함수

- COUNT(expr)
  - expr의 전체 개수를 구해 반환한다.
  - expr로 보통 \*을 많이 사용한다(조회된 데이터 전체를 가져옴)
- MAX(expr)
  - expr의 최댓값을 반환한다
- MIN(expr)
  - expr의 최솟값을 반환한다
- SUM(expr)
  - expr의 합계를 반환한다
- AVG(expr)
  - expr의 평균값을 반환한다
- VARIANCE(expr)
  - expr의 분산을 반환한다
- STDDEV(expr)
  - expr의 표준편차를 반환한다

집계 함수 예제

```sql
SELECT COUNT(*) cnt
      ,MIN(passenger_number) min_value
      ,MAX(passenger_number) max_value
      ,SUM(passenger_number) sum_value
      ,AVG(passenger_number) avg_value
FROM subway_statistics;
```

- 위 쿼리문을 실행해보면 집계 함수가 어떤 기능을 수행하는지 이해할 수 있을 것이다
- 그런데 전체 건수, 전체 승객 수는 일정부분 의미가 있을지 몰라도 최소, 최대, 평균 승하차 인원수는 별 의미가 없다
- 나머지 데이터들도 의미있게 만드려면 집계 함수와 함께 GROUP BY절을 결합해 사용해야 한다

```SQL
SELECT station_name
      ,COUNT(*) cnt
      ,MIN(passenger_number) min_value
      ,MAX(passenger_number) max_value
      ,SUM(passenger_number) sum_value
      ,AVG(passenger_number) avg_value
FROM subway_statistics
WHERE gubun='승차'
GROUP BY station_name
ORDER BY station_name;
```

- 이렇게 집계 함수와 GROUP BY절을 결합하니 지하철역별로 승차인원 정보를 확인할 수 있게 됐다
- 결과를 확인해보면 구로디지털단지역의 승차 인원이 많은 편인 것을 확인할 수 있다
- 보다 세밀하게 들여다보기 위해 GROUP BY절에 컬럼을 추가해보자

```SQL
SELECT station_name
      ,boarding_time
      ,gubun
      ,COUNT(*) cnt
      ,MIN(passenger_number) min_value
      ,MAX(passenger_number) max_value
      ,SUM(passenger_number) sum_value
      ,AVG(passenger_number) avg_value
FROM subway_statistics
WHERE station_name in ('구로디지털단지(232)')
GROUP BY station_name, boarding_time, gubun
ORDER BY station_name, boarding_time, gubun;
```

- 결과를 확인해보면 전반적으로 승하차 인원이 많지만 특히 8~9시에 급격히 증가했음을 확인할 수 있다

승하차 인원이 많은 순서로 조회하기

```sql
SELECT station_name
      ,boarding_time
      ,gubun
      ,MIN(passenger_number) min_value
      ,MAX(passenger_number) max_value
      ,SUM(passenger_number) sum_value
FROM subway_statistics
GROUP BY station_name, boarding_time, gubun
ORDER BY 6 DESC;
```

- 전체 역을 대상으로 하므로 WHERE절을 없앴다
- ORDER BY 6 DESC는 SELECT 절의 6번째 항목인 sum_value별로 내림차순으로 정리함을 의미한다

HAVING절

- 만약 승차인원이 15000~16000인 역만 조회하려면 어떻게 해야할까
- HAVING절은 GROUP BY절과 함께 사용하며 집계 함수 결과 값으로 조건을 걸 때 사용한다

```SQL
SELECT station_name
      ,boarding_time
      ,gubun
      ,MIN(passenger_number) min_value
      ,MAX(passenger_number) max_value
      ,SUM(passenger_number) sum_value
FROM subway_statistics
GROUP BY station_name, boarding_time, gubun
HAVING SUM(passenger_number) BETWEEN 15000 AND 16000
ORDER BY 6 DESC;
```

- 종각, 양재, 역삼 3개 역이 결과로 조회될 것이다
