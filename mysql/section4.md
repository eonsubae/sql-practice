# Section 4. Inserting Data (and a couple other things)

### lecture 44. Inserting Data

INSERT
```sql
INSERT INTO cats(name, age)
VALUES ('Jetson', 7);
```
* INSERT INTO 데이터를 집어넣을 테이블 이름 (컬럼명1, 컬럼명2, ...)
* VALUES (지정한 컬럼에 맞는 데이터를 입력)
* 순서만 맞춰서 작성한다면 파라미터로 넘긴 name, age의 순서는 변경해도 된다

---

### lecture 46. Super Quick Intro To SELECT

테이블에 삽입한 데이터 확인하기
```sql
SELECT * FROM cats;
```
* cats 테이블에 있는 모든 데이터를 가져온다

---

### lecture 48. Multiple INSERT

테이블에 데이터 여러개 삽입하기
```sql
INSERT INTO cats(name, age)
VALUES ('Charlie', 10),
       ('Sadie', 3),
       ('Lazy Bear', 1);
```
* 콤마(,)를 구분자로 여러개의 데이터를 한번에 삽입할 수 있다

---

### lecture 54. MySQL Warnings

앞서 작성한 cats 테이블의 name 컬럼은 50자인 문자열로 한정되어 있다
* 만약 50자 이상을 입력하면 로우가 추가되면서 warning도 추가된다

다음 명령어로 발생한 warning을 볼 수 있다
```sql
SHOW WARNINGS;
```

---

### lecture 56. NULL and NOT_NULL

앞서 desc 테이블명; 을 사용해서 테이블의 구조를 볼 수 있었다
* 그런데 필드와 타입 외에도 Null, Key, Default, Extra 같은 컬럼이 있다
* 이 컬럼들의 의미에 대해서 알아보자

Null
* 값을 지정하지 않았음을 의미한다
* YES로 되어 있는 경우 값을 지정하지 않은 상태를 허용한다
* 주의할점은 숫자 형태의 타입을 가진 필드의 경우 Null을 0으로 오해하는 사람들이 많다
* 그러나 Null은 값이 지정되지 않았음을 알려줄 뿐이지 0이 아니다

cats 테이블에 name 컬럼만 추가해보기
```sql
INSERT INTO cats(name)
VALUES ('Alabama');
```
* SELECT * FROM cats; 명령어로 추가된 로우를 확인해보자
* name 컬럼에는 Alabama가 추가되고, age 컬럼은 NULL이 추가되어 있음을 확인할 수 있다

cats 테이블에 아무런 데이터도 추가하지 않는다면
```sql
INSERT INTO cats()
VALUES ();
```
* name과 age에 모두 NULL이 추가된다
* 이를 방지하기 위해서는 NOT NULL을 사용해야 한다

NOT NULL
```sql
CREATE TABLE cats2
(
    name VARCHAR(100) NOT NULL,
    age  INT          NOT NULL
);
```
* 데이터 타입 뒤에 NOT NULL을 지정하면 NULL이 방지된다
* 이렇게 NOT NULL을 추가한 필드는 앞선 예제처럼 데이터 삽입시 생략할 수 없다

---

### lecture 58. Setting Default Values

Default value 지정하기
```sql
CREATE TABLE cats3
(
    name VARCHAR(100) DEFAULT 'unnamed',
    age  INT          DEFAULT 99
);
```
* 이제 name에 값을 지정하지 않으면 unnamed가 기본값으로 추가된다
* 같은 방식으로 age에 값을 지정하지 않으면 99가 기본값으로 추가된다

이제 name과 age를 지정하지 않고 데이터를 추가해보면 기본 값이 자동으로 채워짐을 확인할 수 있다
```sql
INSERT INTO cats3()
VALUES ();

SELECT * FROM cats3;
```

not null과 default value 조합하기
* default value를 지정하면 값을 지정하지 않았을 때 자동으로 기본값이 추가된다
* 그렇다면 not null을 동시에 지정할 필요는 없는게 아니냐는 의문이 제기된다
* 하지만 수동으로 null을 지정하지 못하게 해야하는 요구사항이 있을때는 이 둘을 조합해야 한다
```sql
CREATE TABLE cats4
(
    name VARCHAR(100) NOT NULL DEFAULT 'unnamed',
    age  INT          NOT NULL DEFAULT 99
);
```

---

### lecture 60. A Primer On Primary Keys

desc 테이블명; 으로 테이블 구조를 확인해보면 Key라는 컬럼이 있다
* 만약 추가한 로우들의 데이터가 같은 것들이 여러개라면 이를 구분해야할 필요가 생긴다
* 이 때 Primary Key라는 유니크한 구분자를 사용해서 쉽게 구분할 수 있다
* PK를 지정하면 이후에 할당한 모든 데이터에 자동으로 할당된다

예시
```sql
INSERT INTO cats(name, age) VALUES ('Helena' 6);
```
* 위 쿼리를 세 번 입력하고 SELECT * FROM cats;로 확인해보면 동일한 값을 가진 데이터가 3개 추가되어있다
* 그러나 앞서 만든 테이블 구조 하에서는 이를 구분할 수 없다

PK를 지정해서 테이블 만들기
```sql
CREATE TABLE unique_cats (cat_id INT NOT NULL
                      ,name VARCHAR(100)
                      ,age INT
                      ,PRIMARY KEY (cat_id));
```
* 유니크한 구분자(PRIMARY KEY)로 cat_id를 지정했다
* DESC unique_cats로 확인해보면 Key 컬럼에 PRI가 지정되어 있는 것을 확인할 수 있다
* 이제 cat_id 컬럼에 데이터를 추가해서 구분자를 설정할 수 있다

```sql
INSERT INTO unique_cats (cat_id, name, age) VALUES (1, "Fred", 23);
INSERT INTO unique_cats (cat_id, name, age) VALUES (2, "Fred", 23);
INSERT INTO unique_cats (cat_id, name, age) VALUES (3, "Fred", 23);
```
* 같은 name과 age 값을 가진 로우가 3개 추가되었지만 이제는 PK를 이용해 구분할 수 있다
* 만약 PK를 이미 지정된 값과 동일하게 지정하면 경고가 발생한다
* 그런데 이렇게 PK를 수동으로 추가해주는 것을 실용적이지 못하다
* AUTO_INCREMENT를 사용하면 로우가 추가될 때 PK가 자동으로 증가하면서 추가하도록 만들 수 있다

```sql
CREATE TABLE unique_cats2 (cat_id INT NOT NULL AUTO_INCREMENT,
                           name VARCHAR(100),
                           age INT,
                           PRIMARY KEY (cat_id));
```
* AUTO_INCREMENT가 추가된 것을 제외하면 앞서 만들었던 테이블과 동일하다
* 이처럼 PK로 사용할 컬럼에 AUTO_INCREMENT를 추가하면 값을 지정하지 않아도 자동으로 증가한다
* DESC unique_cats2;로 테이블 구조를 확인해보면 Extra 컬럼에 auto_increment가 추가된 것을 확인할 수 있다
  
위 코드에서 PRIMARY KEY를 cat_id에 추가해도 된다
```sql
CREATE TABLE unique_cats2 (cat_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
                           name VARCHAR(100),
                           age INT
                          );
```


다시 데이터를 추가해보자
```sql
INSERT INTO unique_cats2(name, age) VALUES ('Skippy', 4);
INSERT INTO unique_cats2(name, age) VALUES ('Skippy', 4);
INSERT INTO unique_cats2(name, age) VALUES ('Skippy', 4);
```
* 이제 자동으로 유니크한 값을 가진 cat_id 필드가 자동으로 증가하면서 추가된다

---
