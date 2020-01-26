# Section 9 - The Magic of Aggregate Functions

sum, min, max, average 등의 연산에 사용되는 함수들을 학습하는 장

### lecture 138 - The Count Function

count - 얼마나 많은 데이터가 있는지를 알고 싶을 때 유용한 함수
```sql
SELECT COUNT(*) FROM books;
```

얼마나 많은 퍼스트 네임이 있는지 세보기
```sql
SELECT COUNT(DISTINCT author_fname) FROM books;
```
* DISTINCT를 COUNT의 인자에 적용하면 된다

라스트네임은 같지만 퍼스트네임이 다른 경우가 있다
```sql
SELECT COUNT(DISTINCT author_lname, author_fname) FROM books;
```
* author_lname이 같은 것을 제외하되 auhtor_fname이 다르면 제외시키지 않는다

제목이 얼마나 많은 the를 포함하는지 세보기
```sql
SELECT COUNT(*) FROM books WHERE title LIKE '%the%';
```

---

### lecture 140 The Joys of Group By

group by는 count와 같이 사용할 때 상당히 유용하다
```sql
SELECT title, author_lname FROM books GROUP BY author_lname;
```
* 위와 같이 사용하면 라스트네임이 같은 책 중에서 첫번째에 있는 책들만 가져온다
* 이렇게 사용하면 GROUP BY는 별로 유용하지 않아 보인다

count와 같이 사용하기
```sql
SELECT author_lname, COUNT(*) FROM books GROUP BY author_lname;
```
* 라스트네임으로 그룹화해서 각각의 라스트 네임마다 얼마나 많은 책이 있는지를 보여준다

라스트네임은 같지만 퍼스트네임이 다른 경우를 구분하기
```sql
SELECT author_fname, author_lname, COUNT(*) FROM books GROUP BY author_lname, author_fname;
```

년도별로 출간된 책의 갯수를 구하기
```sql
SELECT released_year, COUNT(*) FROM books GROUP BY released_year;
```

---

### lecture 142 - Min and Max Basics

MIN, MAX - 최소값과 최저값을 구할때 사용하는 함수들이다

책이 처음 출간된 년도를 구하기
```sql
SELECT MIN(released_year) FROM books;
```

가장 페이지가 많은 책
```sql
SELECT MAX(pages) FROM books;
```

가장 페이지가 많은 책의 제목을 출력하려면?
```sql
SELECT MAX(pages), title FROM books;
```
* 위처럼 입력하면 출력될 것 같지만
* 가장 많은 페이지 숫자와, 가장 위에 있는 타이틀 하나가 같이 출력된다
* 위 문제는 다음 강의에서 해결한다

--- 

### lecture 144 - SUBQUERIES - A Problem with Min and Max

앞서 직면했던 문제는 서브 쿼리(한 쿼리문 안에 다른 쿼리문을 사용하는 것)를 사용해서 해결할 수 있다
```sql
SELECT pages, title FROM books WHERE pages=(SELECT MAX(pages) FROM books);
```

서브쿼리를 사용하면 다양한 로직을 구현할 수 있으나 데이터가 큰 경우 성능 저하로 이어질 수 있다
* 물론 우리가 다루는 예제처럼 작은 데이터를 다룰 때는 성능저하는 신경쓸 필요가 없다

ORDER BY와 LIMIT을 이용해서 해결하기
```sql
SELECT pages, title FROM books ORDER BY pages DESC LIMIT 1;
```

---

### lecture 146 - Using Max and Min with Group By

각 작가들의 처음 출판한 년도를 가져오기
```sql
SELECT author_fname, author_lname, MIN(released_year) 
FROM books 
GROUP BY author_lname, author_fname; 
```

각 작가들의 가장 긴 페이지 수를 가져오기
```sql
SELECT author_fname, author_lname, MAX(pages) FROM books GROUP BY author_lname, author_fname;
```

---

### lecture 148 - The Sum Function

전체 데이터베이스에 있는 모든 페이지 수를 더하기
```sql
SELECT SUM(pages) FROM books;
```

각 작가마다 저술한 책의 모든 페이지를 구하기
```sql
SELECT CONCAT(author_fname, ' ', author_lname) AS author, SUM(pages) AS 'total page' 
FROM books
GROUP BY author_lname, author_fname;
```

---

### lecture 150 - The Avg Function

AVG - 평균을 구하는 함수

모든 책의 평균 출판년을 구하기
```sql
SELECT AVG(released_year) FROM books;
```

같은 년도에 출판된 책들의 평균 재고량을 구하기
```sql
SELECT released_year, AVG(stock_quantity) FROM books GROUP BY released_year ASC;
```

---

### lecture 152 - Aggregate Functions Challenges

데이터베이스에 입력된 책의 수를 출력하기
```sql
SELECT COUNT(*) FROM books;
```

각 년도마다 몇 권의 책이 출간되었는지 출력하기
```sql
SELECT released_year, COUNT(*) AS 'released books count' FROM books GROUP BY released_year;
```

모든 책의 재고량을 더한 숫자를 구하기
```sql
SELECT SUM(stock_quantity) FROM books;
```

각 작가의 평균 출판 년도를 구하기
```sql
SELECT CONCAT(author_fname, ' ', author_lname), AVG(released_year) 
FROM books
GROUP BY author_lname, author_fname;
```

가장 긴 책을 쓴 작가의 풀네임을 구하기
```sql
SELECT CONCAT(author_fname, ' ', author_lname) AS 'full name'
FROM books
ORDER BY pages DESC LIMIT 1;
```

출간 년도, 출간된 책의 수, 평균 페이지를 오름차 순으로 출력하기
```sql
SELECT released_year AS 'year', COUNT(*) AS '# of books', AVG(pages) AS 'avg pages'
FROM books
GROUP BY released_year ASC;
```