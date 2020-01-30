# 데이터 입력과 삭제 그리고 수정

기본적인 INSERT구문
```SQL
INSERT INTO 테이블 명 ( column1, column2, column3, ...)
VALUES ( 값1, 값2, 값3, ...);
```
* 그런데 이렇게 데이터를 입력하면 여러 개의 데이터를 추가해야 할 때 중복된 문장을 작성해야 한다
* SELECT문과 INSERT문을 결합하면 한 번에 여러 개의 데이터를 입력할 수 있다
* VALUES를 SELECT문이 대체하는 형태가 된다

실습을 위해 지하철 통계 테이블을 동일한 구조로 복사해 새로운 테이블 작성하기
```sql
CREATE TABLE subway_dml_test AS
SELECT *
  FROM subway_statistics 
 WHERE ROWNUM < 1;

ALTER TABLE subway_dml_test
ADD PRIMARY KEY ( seq_id ) ;
```

종합운동장 데이터만 입력해보기
```sql
INSERT INTO subway_dml_test
SELECT *
FROM subway_statistics
WHERE station_name LIKE '종합운동장%';

COMMIT;
```

종합운동장 데이터만 제외하고 입력해보기
```SQL
INSERT INTO subway_dml_test
SELECT *
FROM subway_statistics
WHERE station_name NOT LIKE '종합운동장%';

COMMIT;
```

만약 기본키만 충돌하지 않도록 입력하려고 한다면
```SQL
INSERT INTO subway_dml_test
SELECT a.*
  FROM subway_statistics a
 WHERE NOT EXISTS ( SELECT 1
                      FROM subway_dml_test b
                     WHERE a.seq_id = b.seq_id );
```

역이름이 종합운동장인 데이터 삭제하기
```SQL
DELETE FROM subway_dml_test
WHERE station_name LIKE '종합운동장%';

COMMIT;
```

데이터 수정 - UPDATE문
```SQL
UPDATE 테이블 명
SET column1 = 값1
   ,column2 = 값2
   ,...
WHERE 절;
```
* 우선 수정할 대상 테이블을 UPDATE 키워드 다음에 명시한다
* 그 다음 수정할 컬럼과 값을 SET 키워드 다음에 콤마(,)로 연결하며 수정한다
* WHERE절로 수정할 데이터를 골라낼 적절한 조건을 지정한다

subway_dml_test 테이블에서 삼성역 데이터 중 boarding_date와 passenger_number컬럼 값을 변경하기
* boarding_date는 기존 값에서 한달을 더한다
* passenger_number는 기존 값에 10을 더한다

```sql
UPDATE subway_dml_test
SET passenger_number = passenger_number + 10
   ,boarding_date = ADD_MONTHS(boarding_date, 1)
WHERE station_name LIKE '삼성%';

COMMIT;
```