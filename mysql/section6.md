# Section 6. CRUD Challenge Section

shirts_db 데이터베이스 생성하기
```sql
CREATE DATABASE shirts_db;
```

생성한 데이터베이스 사용
```sql
USE shirts_db;

SELECT database();
```
* SELECT database();는 사용중인지 확인하는 명령어다

shirts 테이블 생성하기
```sql
CREATE TABLE shirts
(
  shirt_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
  article VARCHAR(20),
  color VARCHAR(20),
  shirt_size VARCHAR(5),
  last_worn INT
);
```
* PRIMARY KEY는 shirt_id와 다른 라인에서 지정해도 된다
  - PRIMARY KEY (shirt_id)

데이터 한번에 삽입하기
```sql
INSERT INTO shirts VALUES
('t-shirt', 'white', 'S', 10),
('t-shirt', 'green', 'S', 200),
('polo shirt', 'black', 'M', 10),
('tank top', 'blue', 'S', 50),
('t-shirt', 'pink', 'S', 0),
('polo shirt', 'red', 'M', 5),
('tank top', 'white', 'S', 200),
('tank top', 'blue', 'M', 15);
```

데이터 하나 삽입하기
* 데이터는 polo shirt, purple, M, 50이다
```sql
INSERT INTO shirts(article, color, shirt_size, last_worn) VALUES
('polo shirt', 'purple', 'M', 50);
```

모든 셔츠의 article과 color를 조회하기
```sql
SELECT article, color FROM shirts;
```

shirt_id를 제외하고 M 사이즈인 셔츠 모두 조회하기
```sql
SELECT article, color, shirt_size, last_worn FROM shirts WHERE shirt_size='M';
```

모든 polo shirt의 사이즈를 L로 갱신하기
```sql
UPDATE shirts SET shirt_size='L' WHERE article='polo shirt';
```

last_worn이 15인 셔츠를 0으로 바꾸기
```sql
UPDATE shirts SET last_worn=0 WHERE last_worn=15;
```

모든 white shirts의 사이즈를 XS, 색상을 off white로 변경하기
```sql
UPDATE shirts SET shirt_size='XS', color='off white' WHERE color='white';
```

last_worn이 200인 모든 셔츠 삭제하기
```sql
DELETE FROM shirts WHERE last_worn=200;
```

모든 tank top 삭제하기
```sql
DELETE FROM shirts WHERE article='tank top';
```

모든 셔츠 삭제하기
```sql
DELETE FROM shirts;
```

shirts 테이블 자체를 삭제하기
```sql
drop table shirts;
```