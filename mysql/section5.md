# Section 5. CRUD commands

### lecture 66. Introduction to CRUD

CRUD
* Create - 데이터 추가
* Read - 데이터 읽어오기
* Update - 데이터 갱신
* Delete - 데이터 삭제

Create
* 앞서 INSERT INTO를 사용해서 Create를 했었다
* 이 섹션의 나머지 강의들에서는 RUD에 집중한다

앞서 만들었던 cats 테이블을 삭제하고 다시 만들어보자
```sql
DROP TABLE cats;

CREATE TABLE cats
(
  cat_id INT NOT NULL AUTO_INCREMENT,
  name VARCHAR(100),
  breed VARCHAR(100),
  age INT,
  PRIMARY KEY(cat_id)
);
```

---

### lecture 70. Official Introduction to SELECT

Read
* 앞서 SELECT * FROM cats;로 cats 테이블에 있는 모든 데이터를 조회한 바 있다
* SELECT는 어떤 컬럼을 원하냐는 의미로 해석할 수 있다
* *는 존재하는 모든 컬럼을 가져오게끔 한다
  
cats 테이블에서 name 컬럼만 조회하기
```sql
SELECT name FROM cats;
```
* cat_id, breed, age를 추가로 조회하고 싶으면 콤마(,)를 구분자로 사용하면 된다

요구사항에 따른 조회 예시
```sql
SELECT * FROM cats; 

SELECT name FROM cats; 

SELECT age FROM cats; 

SELECT cat_id FROM cats; 

SELECT name, age FROM cats; 

SELECT cat_id, name, age FROM cats; 

SELECT age, breed, name, cat_id FROM cats; 

SELECT cat_id, name, age, breed FROM cats; 
```

---

### lecture 72. Introduction to WHERE

신중하게 쿼리 날리기
* 앞서 SELECT로 특정 칼럼에 있는 모든 데이터를 가져오는 방식으로만 쿼리를 작성했다
* 그러나 현실에서 수 많은 데이터(ex. 몇만개)를 매번 모두 불러오는 것은 유용하지 않다
* 따라서 특정한 조건을 지정해 불러올 데이터를 한정해야 한다

WHERE
* 특정 데이터만 가져올 수 있도록 도와주는 절(clause)이다

age가 4인 데이터만 불러오기
```sql
SELECT * FROM cats WHERE age=4;
```

Egg가 name인 데이터만 불러오기
```sql
SELECT * FROM cats WHERE name='Egg';
```
* Egg를 egG나 egg 등으로 바꿔도 같은 결과가 나온다

---

### lecture 77. Introduction to Alias

Alias
* 데이터를 보다 읽기 쉽게 만들기 위해 사용하는 명령어
* 앞서 만들었던 cats 테이블의 age 컬럼을 year로 바꿔서 보여주고 싶은 요구사항이 있을 수 있다

예시
```sql
SELECT cat_id AS id, name FROM cats;

SELECT name AS 'cat name' breed AS 'kitty breed' FROM cats;
```
* cat_id를 id로 바꿔서 보여준다
* 중간에 공백을 넣고 싶으면 ''로 감싸줘야 한다

---

### lecture 79. the UPDATE Command

Update
* 이미 존재하는 데이터를 변경해야 하는 요구사항이 있을 수 있다
* 항상 갱신하기 전에 SELECT로 갱신하려는 데이터가 자신이 생각하는 데이터가 맞는지를 먼저 확인하자

cats 테이블에서 breed가 Tabby인 데이터를 가져와서 Shorthair로 변경하기
```sql
UPDATE cats SET breed='Shorthair'
WHERE breed='Tabby';
```

cat 테이블에서 name이 Misty인 데이터의 age를 14로 변경하기
```sql
UPDATE cats SET age=14
WHERE name='Misty';
```

---

### lecture 84. Introduction to DELETE

Delete
* 데이터를 삭제하기
* 항상 삭제하기 전에 SELECT로 삭제하려는 데이터가 자신이 생각하는 데이터가 맞는지를 먼저 확인하자

cats 테이블에서 name이 Egg인 데이터를 삭제하기
```sql
SELECT * FROM cats WHERE name='Egg';

DELETE FROM cats WHERE name='Egg';
```

cats 테이블에 있는 모든 데이터 삭제하기
```sql
DELETE FROM cats;
```
* 테이블을 DROP하는 것과는 다르다
* 테이블 구조는 남겨놓되 테이블에 존재하는 데이터들만 없애는 것이다

---