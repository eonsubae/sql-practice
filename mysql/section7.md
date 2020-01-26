# Sectino 7 - The World Of String Functions

문자열을 다루는 함수들을 다루는 장
* 반환되는 문자열의 형태를 바꾸는 것이지 실제 데이터를 바꾸는 것은 아니다

### lecture 103 - Loading Our Book Data

books 테이블 구성을 살펴보자
```sql
create table books (
  book_id int not null auto_increment,
  title varchar(100),
  author_fname varchar(100),
  author_lname varchar(100),
  released_year int,
  stock_quantity int,
  pages int,
  primary key (book_id)
);
```

그런 다음 아마존에서 검색한 실제 책 정보들을 books 테이블에 넣어보자
```sql
insert into books (title, author_fname, author_lname, released_year, stock_quantity, pages) 
values ('The Namesake', 'Jhumpa', 'Lahiri', 2003, 32, 291),
('Norse Mythology', 'Neil', 'Gaiman',2016, 43, 304),
('American Gods', 'Neil', 'Gaiman', 2001, 12, 465),
('Interpreter of Maladies', 'Jhumpa', 'Lahiri', 1996, 97, 198),
('A Hologram for the King: A Novel', 'Dave', 'Eggers', 2012, 154, 352),
('The Circle', 'Dave', 'Eggers', 2013, 26, 504),
('The Amazing Adventures of Kavalier & Clay', 'Michael', 'Chabon', 2000, 68, 634),
('Just Kids', 'Patti', 'Smith', 2010, 55, 304),
('A Heartbreaking Work of Staggering Genius', 'Dave', 'Eggers', 2001, 104, 437),
('Coraline', 'Neil', 'Gaiman', 2003, 100, 208),
('What We Talk About When We Talk About Love: Stories', 'Raymond', 'Carver', 1981, 23, 176),
("Where I'm Calling From: Selected Stories", 'Raymond', 'Carver', 1989, 12, 526),
('White Noise', 'Don', 'DeLillo', 1985, 49, 320),
('Cannery Row', 'John', 'Steinbeck', 1945, 95, 181),
('Oblivion: Stories', 'David', 'Foster Wallace', 2004, 172, 329),
('Consider the Lobster', 'David', 'Foster Wallace', 2005, 92, 343);
```
* 입력된 정보 중 일부를 함수를 이용해 변화시킨 형태로 출력할 수 있다
* 구글에 mysql string fucntions로 검색해보면 공식문서에 자세한 정보들이 나와있다

저자의 fullname을 알고 싶은 요구사항이 생겼을 때 - CONCAT(column, another_column, ...)
```sql
select CONCAT(author_fname, author_lname) from books;
```
* 위와 같이 입력하면 firstname과 lastname의 중간에 공백이 없어 이상하게 보인다

공백을 추가하려면
```sql
select CONCAT(author_fname, ' ', author_lname) from books;
```
* 공백인 인자를 중간에 하나 더 추가하면 된다

공백이 추가되어 원하는 방식대로 fullname이 보이지만 테이블 이름에 CONCAT 함수와 인자들이 노출되어 보기 불편하다
```sql
select CONCAT(author_fname, ' ', author_lname) AS fullname from books;
```
* AS를 이용해서 테이블 이름을 바꿔주면 한결 보기 편해진다

CONCAT_WS 함수 사용하기 - Concat with seperator
* 구분자(seperator)를 사용해 문자열을 합칠 때 편리한 함수다
* 대쉬(-)를 구분자로 사용해 '제목 - 작가의 first name - 작가의 last name' 형식으로 데이터를 정렬하고 싶다면?
```sql
select CONCAT_WS(' - ', title, author_fname, author_lname) AS 'title - author name' from books;
```

---

### lecture 107 - Introducing Substring

SUBSTRING은 문자열의 일부를 뽑아주는 함수다
```sql
SELECT SUBSTRING('Hello World', 1, 4);
```
* Hell이 추출된다
* 일반적인 프로그래밍 언어들과 다르게 인덱스가 1부터 시작함을 주의해야한다

인자가 두 개인 경우
```sql
SELECT SUBSTRING('Hello World', 7);
```
* 1부터 7까지 출력될 것이라고 생각하기 쉽다
* 그러나 결과는 World가 출력된다
* SUBSTRING 함수는 인자가 2개일 때, 두번째 인자의 인덱스 문자열부터 끝까지 출력한다
  
음수 인덱스도 사용 가능하다
```sql
SELECT SUBSTRING('Hello World', -3);
```
* rld가 출력된다

SUBSTR함수는 SUBSTRING과 동일하게 동작한다
* 타이핑이 귀찮다면 SUBSTR을 사용하자
* 그러나 데이터베이스마다 축약함수의 이름이 SUBSTR, SUBST, SBST 등으로 다양해서 쿼리의 이식성은 나빠질 수 있다

제목을 맨 앞 10자만 뽑되 끝에 ...을 추가하기
```sql
SELECT CONCAT(SUBSTR(title, 1, 10), '...') as 'short title' from books;
```
* 앞서 배웠던 CONCAT과 SUBSTR을 조합하면 된다

---

### lecture 109 - Introducing REPLACE

REPLACE는 문자열의 일부를 제거하거나 특정 문자로 대체할 때 많이 사용하는 함수다
```sql
SELECT REPLACE('Hello World', 'Hell', '#$%#');
```
* 문자열 Hell을 #$%#로 대체한다

공백을 ' and '로 대체하기
```sql
SELECT REPLACE('cheese bread coffee milk', ' ', ' and ');
```
* cheese and bread and coffee and milk로 변환된다

책의 제목에 있는 문자 e를 3으로 변경하기
```sql
SELECT REPLACE(title, 'e', 3) from books;
```

---

### lecture 111 - Using REVERSE

문자열을 거꾸로 바꾸는 함수
```sql
SELECT REVERSE('Hello World');
```

책 제목으로 palindrome 만들기
```sql
SELECT CONCAT(title, REVERSE(title)) from books;
```

---

### lecture 113 - Working with CHAR LENGTH

문자열의 길이를 반환하는 함수
```sql
SELECT author_lname, CHAR_LENGTH(author_lname) AS 'lastname length' from books;
```

---

### lecture 115 - Changing Case With UPPER and LOWER

문자열을 대문자 혹은 소문자로 변경하는 함수
```sql
SELECT UPPER('Hello World');
```
* HELLO WORLD가 출력된다

```sql
SELECT LOWER('Hello World');
```
* hello world가 출력된다

### Challenge

1번 - "Why does my cat look at me with such hatred?" - 이 문자열을 거꾸로 뒤집고 대문자로 변경하기
```sql
SELECT UPPER(REVERSE("Why does my cat look at me with such hatred?"));
```

2번 - 다음 SQL문이 어떻게 출력될까 맞춰보기
```sql
SELECT
  REPLACE
  (
  CONCAT('I', ' ', 'like', ' ', 'cats'),
  ' ',
  '-'
  );
```
* I-like-cats

3번 - 입력된 책들의 title에 있는 공백들을 ->로 대체하기
```sql
SELECT REPLACE(title, ' ', '->') AS title from books;
```

4번 - lastname을 순서대로 출력한 것을 forwards로 거꾸로 출력한 것을 backwards로 출력하기
```sql
SELECT author_lname AS forwards, REVERSE(author_lname) AS backwards from books;
```

5번 - fullname을 대문자로 출력하기
```sql
SELECT UPPER(CONCAT(author_fname, ' ', author_lname)) AS 'full name in caps' from books;
```

6번 - 책의 title과 출판 년도를 특정 형식으로 출력하기(title was released in released_year)
```sql
SELECT CONCAT(title, " was released in ", released_year) AS blurb from books;
```

7번 - 책의 제목과 제목의 길이를 출력하기
```sql
SELECT title, CHAR_LENGTH(title) AS 'character count' from books;
```

8번 - 책의 제목을 맨 앞의 10자만 가져와 ...을 붙인것을 short title로 출력하고 저자의 fullname을 ,를 구분자로 변형한 것을 author로 출력하고, 재고를 stock_quantity in stock 형식으로 quantity로 출력하라
```sql
SELECT CONCAT(SUBSTRING(title, 1, 10), '...') AS 'short title', 
       CONCAT_WS(',', author_lname, author_fname) AS author,
       CONCAT(stock_quantity, ' in stock') AS quantity
FROM books;
```