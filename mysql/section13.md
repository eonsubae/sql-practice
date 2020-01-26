# Section 13. Many To Many 

### lecture 224. Many To Many Basics

M : N 관계
* books와 authors
  - 한 권의 책은 여러 저자에 의해 쓰여질 수 있다
  - 한 저자는 여러 권의 책을 저술할 수 있다
* blog posts와 tags
* students 와 classes

tv show reviewing 어플리케이션 예시
* Reviewers Data
  - 리뷰하는 유저에 대한 테이블
  - id, first_name, last_name
* Reviews Data
  - tv show를 리뷰한 정보를 포함하는 테이블
  - id, rating, series_id, reviewer_id
  - tv show에 대한 정보를 가진 series와 리뷰어에 대한 정보를 가진 reviewer_id를 저장한 중앙 저장소 역할을 도맡음
  - series와 reviewer는 reivews를 통해 연결되어 있는 구조다
* Series Data
  - tv show에 대한 테이블
  - id, title, released_year, genre

샘플 데이터
* Reviewers
  - 1, Blue, Steele
  - 2, Wyatt, Earp
* Series
  - 1, Archer, 2009, Animation
  - 2, Fargo, 2014, Drama
* Reivews
  - 1, 8.9, 1, 2
  - 2, 9.5, 2, 2

---

### lecture 225. Creating Our Tables

tv_review_show라는 새로운 데이터 베이스를 만든다
```sql
CREATE DATABASE tv_review_show;

USE tv_review_show;
```

reviewers 테이블 생성하기
```sql
CREATE TABLE reviewers (
  id INT AUTO_INCREMENT PRIMARY KEY,
  first_name VARCHAR(100),
  last_name VARCHAR(100)
);
```

reviews 테이블 생성하기
```sql
CREATE TABLE reviews (
  id INT AUTO_INCREMENT PRIMARY KEY,
  rating DECIMAL(2,1), 
  series_id INT, 
  reviewer_id INT,
  FOREIGN KEY(series_id) REFERENCES series(id),
  FOREIGN KEY(reviewer_id) REFERENCES reviewers(id)
);
```

series 테이블 생성하기
```sql
CREATE TABLE series (
  id INT AUTO_INCREMENT PRIMARY KEY, 
  title VARCHAR(100), 
  released_year YEAR(4),
  genre VARCHAR(100)
);
```

샘플 데이터 집어넣기
```sql
INSERT INTO series (title, released_year, genre) VALUES
    ('Archer', 2009, 'Animation'),
    ('Arrested Development', 2003, 'Comedy'),
    ("Bob's Burgers", 2011, 'Animation'),
    ('Bojack Horseman', 2014, 'Animation'),
    ("Breaking Bad", 2008, 'Drama'),
    ('Curb Your Enthusiasm', 2000, 'Comedy'),
    ("Fargo", 2014, 'Drama'),
    ('Freaks and Geeks', 1999, 'Comedy'),
    ('General Hospital', 1963, 'Drama'),
    ('Halt and Catch Fire', 2014, 'Drama'),
    ('Malcolm In The Middle', 2000, 'Comedy'),
    ('Pushing Daisies', 2007, 'Comedy'),
    ('Seinfeld', 1989, 'Comedy'),
    ('Stranger Things', 2016, 'Drama');
 
 
INSERT INTO reviewers (first_name, last_name) VALUES
    ('Thomas', 'Stoneman'),
    ('Wyatt', 'Skaggs'),
    ('Kimbra', 'Masters'),
    ('Domingo', 'Cortes'),
    ('Colt', 'Steele'),
    ('Pinkie', 'Petit'),
    ('Marlon', 'Crafford');
    
 
INSERT INTO reviews(series_id, reviewer_id, rating) VALUES
    (1,1,8.0),(1,2,7.5),(1,3,8.5),(1,4,7.7),(1,5,8.9),
    (2,1,8.1),(2,4,6.0),(2,3,8.0),(2,6,8.4),(2,5,9.9),
    (3,1,7.0),(3,6,7.5),(3,4,8.0),(3,3,7.1),(3,5,8.0),
    (4,1,7.5),(4,3,7.8),(4,4,8.3),(4,2,7.6),(4,5,8.5),
    (5,1,9.5),(5,3,9.0),(5,4,9.1),(5,2,9.3),(5,5,9.9),
    (6,2,6.5),(6,3,7.8),(6,4,8.8),(6,2,8.4),(6,5,9.1),
    (7,2,9.1),(7,5,9.7),
    (8,4,8.5),(8,2,7.8),(8,6,8.8),(8,5,9.3),
    (9,2,5.5),(9,3,6.8),(9,4,5.8),(9,6,4.3),(9,5,4.5),
    (10,5,9.9),
    (13,3,8.0),(13,4,7.2),
    (14,2,8.5),(14,3,8.9),(14,4,8.9);
```

---

### lecture 227. TV Joins Challenge 1

series의 title과 rating을 15개만 출력하기
```sql
SELECT title, rating FROM series
JOIN reviews
ON series.id = reviews.series_id
LIMIT 15;
```

---

### lecture 229. TV Joins Challenge 2

title과 avg_rating을 출력하기
```sql
SELECT title, AVG(rating) AS avg_rating FROM series
JOIN reviews
ON series.id = reviews.series_id
GROUP BY series.id
ORDER BY avg_rating;
```

---

### lecture 231. TV Joins Challenge 3

first_name, last_name, rating을 출력하기
```sql
SELECT first_name, last_name, rating FROM reviewers
INNER JOIN reviews
ON reviewers.id = reviews.reviewer_id
LIMIT 20;
```

---

### lecture 233. TV Joins Challenge 4

한 번도 리뷰당하지 않은 작품의 title을 unreviewed_series로 출력하기
```sql
SELECT title AS unreviewed_series FROM series
LEFT JOIN reviews
ON series.id = reviews.series_id
WHERE rating IS NULL;
```

---

### lecture 235. TV Joins Challenge 5

genre(series)의 평균값인 avg_rating(reviews) 출력하기
```sql
SELECT genre, ROUND(AVG(rating), 2) AS avg_rating FROM series
INNER JOIN reviews
ON series.id = reviews.series_id
GROUP BY genre;
```

---

### lecture 237. TV Joins Challenge 6

first_name, last_name, COUNT(리뷰 횟수), MIN, MAX, AVG, STATUS(리뷰를 한 번이라도 하면 ACTIVE 아니면 INACTIVE) 출력하기
```sql
SELECT 
  first_name, 
  last_name, 
  COUNT(reviews.id) AS 'COUNT', 
  IFNULL(MIN(rating), 0) AS 'MIN',
  IFNULL(MAX(rating), 0) AS 'MAX',
  ROUND(IFNULL(AVG(rating), 0), 2) AS 'AVG',
  IF(COUNT(reviews.id) >= 1, 'ACTIVE', 'INACTIVE') AS 'STATUS'
FROM reviewers
LEFT JOIN reviews
ON reviewers.id = reviews.reviewer_id
GROUP BY reviewers.id;
```

---

### lecture 239. TV Joins Challenge 7 

title, rating, reviewer 출력하기
```sql
SELECT title, rating, CONCAT(first_name, ' ', last_name) AS reviewer
FROM reviewers
INNER JOIN reviews
ON reviewers.id = reviews.reviewer_id
INNER JOIN series
ON reviews.series_id = series.id
ORDER BY title
LIMIT 15;
```