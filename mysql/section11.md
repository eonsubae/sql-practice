# Section 11 - The Power of Logical Operators

### lecture 179. Not Equal

2017년에 출판되지 않은 모든 책의 제목과 출판년도를 가져오기
```sql
SELECT title, released_year FROM books WHERE released_year!=2017;
```

라스트네임이 Harris인 사람을 제외하고 책의 이름과 라스트네임을 가져오기
```sql
SELECT title, author_lname FROM books WHERE author_lname!='Harris';
```

---

### lecture 181. Not Like

앞서 LIKE를 이용해서 패턴매칭을 사용한 바 있다
```sql
SELECT title FROM books WHERE title LIKE 'W%';
```
* title이 W로 시작하는 모든 책들의 제목을 가져오기

NOT LIKE를 사용하면 위 로직을 반대로 수행할 수 있다
```sql
SELECT title FROM books WHERE title NOT LIKE 'W%';
```
* title이 W로 시작하지 않는 모든 책들의 제목을 가져오기

---

### lecture 183. Greater Than

2000년 이후로 출간된 책들의 정보를 모두 가져오기
```sql
SELECT * FROM books WHERE released_year > 2000;
```

같거나 큰 정보를 가져올 때(Greater Than Or Equal To)
* '>='연산자를 사용하면 된다
```sql
SELECT * FROM books WHERE released_year > 2000;
```
* 2000년을 포함하고 이후로 출간된 책들의 정보를 모두 가져오기

다음 쿼리문의 결과는?
```sql
SELECT 99 > 1;
```
* 1이 출력된다
  - True가 1이고, False는 0이기 때문

---

### lecture 185. Less Than

위에서 설명한 Greater Than의 반대다
```sql
SELECT * FROM books WHERE released_year < 2000;
SELECT * FROM books WHERE released_year <= 2000;
```

---

### lecture 187. Logical AND

Dave Eggers가 쓴 책 중에서 2010년 이후로 출판된 책들을 가져오기
```sql
SELECT title, CONCAT(author_fname, ' ', author_lname) AS author, released_year
FROM books
WHERE author_fname='Dave' && author_lname='Eggers' && released_year > 2010;
```
* && 연산자는 AND로 대체할 수 있다

위 조건에서 title에 novel이 들어가는 책들만 가져오기
```sql
SELECT title, CONCAT(author_fname, ' ', author_lname) AS author, released_year
FROM books
WHERE author_fname='Dave' AND author_lname='Eggers' AND released_year > 2010 AND title LIKE '%novel%';
```

---

### lecture 189. Logical OR

라스트네임이 Eggers이거나 2010년 이후로 출판된 책들을 가져오기
```sql
SELECT title, author_lname, released_year AS author, released_year
FROM books
WHERE author_lname='Eggers' || released_year > 2010;
```

---

### lecture 191. Between

2004년~2015년 사이에 출판된 모든 책들을 가져오기
```sql
SELECT title, released_year FROM books WHERE released_year >= 2004 && released_year <= 2015;
```
* 앞서 배운 방법들로만 사용하면 위와 같이 쿼리문을 작성해야 한다

BETWEEN X AND y을 사용하기
```sql
SELECT title, released_year FROM books WHERE released_year BETWEEN 2004 AND 2015;
```

NOT BETWEEN x AND y은 위의 반대로 작동한다
* 2004년 이전에 출간되었거나
* 2015년 이후에 출간된 책들을 가져다준다

1980년 1월 1일 ~ 2000년 1월 1일 사이에 출생한 사람을 가져오기
```sql
SELECT name, birthdt FROM people WHERE birthdt BETWEEN '1980-01-01' AND '2000-01-01';
```
* 그런데 위에 작성된 것처럼 날짜를 작성했을 때 자료형 문제로 원하는 결과가 나오지 않을 수 있다

CAST를 이용해서 DATETIME 자료형으로 변환하기
```sql
SELECT name, birthdt FROM people 
WHERE birthdt BETWEEN CAST('1980-01-01' AS DATETIME) AND CAST('2000-01-01' AS DATETIME);
```

---

### lecture 193. In And Not In

다음 작가들에 의해 쓰여진 모든 책들을 가져오기 - Carver, Lahiri, Smith, ...
```sql
SELECT title, author_lname FROM books 
WHERE author_lname='Carver' OR
      author_lname='Lahiri' OR
      author_lname='Smith';
```
* OR(||) 연산자로 모든 이름을 지정해주면 해결할 수는 있다
* 하지만 너무 번거롭다

IN 연산자를 사용해 반복을 없애기
```sql
SELECT title, author_lname FROM books
WHERE author_lname IN ('Carver', 'Lahiri', 'Smith');
```

1985, 2017년에 출간된 책들을 가져오기
```sql
SELECT title, released_year FROM books
WHERE released_year IN (1985, 2017);
```

NOT IN을 사용하면 IN의 반대로 작동한다

---

### lecture 195. Case Statements

2000년 이전에 출간된 책의 장르는 20th Century Lit으로 이후에 출간된 책의 장르는 Modern Lit으로 가져오기(+제목, 출판년)
```sql
SELECT title, released_year, 
  CASE
    WHEN released_year >= 2000 THEN 'Modern Lit'
    ELSE '20th Century Lit' 
  END AS GENRE
FROM books;
```

재고량을 기준으로 STOCK을 가져오기(50개 미만은 별 하나, 50개 이상은 별 2개, 100개 이상은 별 3개)
```sql
SELECT title, stock_quantity,
  CASE
    WHEN stock_quantity BETWEEN 0 AND 50 THEN '*'
    WHEN stock_quantity BETWEEN 51 AND 100 THEN '**'
    ELSE '***'
  END AS STOCK
FROM books;
```

---

다음 쿼리문들을 평가해라(무엇이 출력될지 답하라는 의미)
```sql
SELECT 10 != 10;
```
* 0이 출력된다(False이므로)

```sql
SELECT 15 > 14 && 99 - 5 <= 94;
```
* 1이 출력된다(True)

```sql
SELECT 1 IN (5, 3) || 9 BETWEEN 8 AND 10;
```
* 1이 출력된다(후자가 True)

1980년 이전에 출간된(1980년도 출간된 책은 제외) 모든 책을 가져오기
```sql
SELECT title, released_year FROM books WHERE released_year < 1980;
```

Eggers 혹은 Chabon이 쓴 모든 책을 가져오기
```sql
SELECT title, CONCAT(author_fname, ' ', author_lname) AS author
FROM books 
WHERE author_lname IN ('Eggers', 'Chabon');
```

Lahiri가 2000년 이후에 출판한 모든 책을 가져오기
```sql
SELECT title, CONCAT(author_fname, ' ', author_lname) AS author, released_year
FROM books
WHERE author_lname = 'Lahiri' AND released_year > 2000;
```

페이지 수가 100~200 사이인 모든 책을 가져오기
```sql
SELECT title, pages
FROM books
WHERE pages BETWEEN 100 AND 200;
```

저자의 라스트네임이 C 혹은 S로 시작하는 모든 책을 가져오기
```sql
SELECT title, CONCAT(author_fname, ' ', author_lname) AS author, released_year
FROM books
WHERE author_lname LIKE 'C%' || author_lname LIKE 'S%';
```

책 제목이 stories를 포함하면 Short Stories를, Just Kids나 A Heartbreaking Work를 포함하면 Memoir를 나머지는 Novel을 출력하도록 TYPE컬럼을 만들기(+제목, 라스트네임)
```sql
SELECT title, author_lname,
  CASE 
    WHEN title LIKE '%stories%' THEN 'Short Stories'
    WHEN title LIKE '%Just Kids%' || title LIKE '%A Heartbreaking Work%' THEN 'Memoir'
    ELSE 'Novel'
  END AS TYPE
FROM books;
```

작가의 제목, 라스트네임, COUNT를 출력한다. COUNT는 각 작가가 출간한 책 권수이고 1권이면 단수를 2권 이상은 복수(s)를 사용한다
```sql
SELECT title, CONCAT(author_fname, ' ', author_lname) AS author,
  CASE
    WHEN COUNT(*) = 1 THEN '1 book'
    ELSE CONCAT(COUNT(*), ' books')
  END AS 'COUNT'
FROM books
GROUP BY author_lname, author_fname
ORDER BY author_lname;
```