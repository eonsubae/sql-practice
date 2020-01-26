# Section 8 - Refining Out Selections

### lecture 124 - Using DISTINCT

특정 컬럼의 데이터들에서 중복을 제거하고 싶을 때 DISTINCT를 사용한다
```sql
SELECT DISTINCT author_lname FROM books;
```
* 작가의 라스트 네임을 중복없이 출력해준다

그런데 만약 라스트 네임은 같지만 퍼스트 네임이 다른 사람이 있는 경우라면?
* 풀네임을 찾아서 중복을 제거하는 방법을 찾아야 한다
* 앞서 string function에서 배웠던 concat을 활용하면 된다
```sql
SELECT DISTINCT(CONCAT(author_fname, ' ', author_lname)) FROM books;
```
* 위에서 작성한 sql처럼 합치지 않고도 중복을 검사한 결과를 얻을 수 있다
```sql
SELECT DISTINCT author_fname, author_lname FROM books;
```
* CONCAT을 사용할 것인지 따로 출력할 것인지는 원하는 결과가 어떤 것이냐에 따라 다르다

---

### lecture 126 - Sorting Data with ORDER BY

데이터를 특정한 방식으로 정렬하기
* ex)books의 경우에는 재고 컬럼을 활용해 베스트 셀러 혹은 워스트 셀러 등으로 정렬할 수 있을 것이다

알파벳 순으로 작가의 라스트 네임 출력하기
```sql
SELECT author_lname FROM books ORDER BY author_lname; 
```

알파벳 순으로 제목 출력하기
```sql
SELECT title FROM books ORDER BY title;
```

라스트 네임과 제목을 알파벳 역순으로 출력하려면 뒤에 DESC를 붙이면 된다
```sql
SELECT author_lname FROM books ORDER BY author_lname DESC; 
```
```sql
SELECT title FROM books ORDER BY title DESC;
```

출간일을 오름차순으로 정렬하기
```sql
SELECT released_year FROM books ORDER BY released_year;
```
* 내림차순으로 정렬하는 것은 위와 마찬가지로 끝에 DESC를 붙이면 된다

ASC(오름차순-ascending)을 이용해서 출간일을 오름차순으로 정렬하기
```sql
SELECT released_year FROM books ORDER BY released_year ASC;
```

숫자를 이용해 정렬할 대상을 지정하기
```sql
SELECT title, author_fname, author_lname FROM books ORDER BY 2;
```
* 출력 대상 중 두번째에 위치한 author_fname 순으로 자료를 정렬해서 출력한다

퍼스트 네임과 라스트 네임을 여러 번 정렬해서 출력하기
```sql
SELECT author_fname, author_lname FROM books ORDER BY author_lname, author_fname;
```
* ORDER BY 절 뒤에 있는 순서대로 정렬한다
* 첫 번째에 입력된 라스트 네임 순으로 먼저 졍렬한 뒤
* 그 다음 입력된 퍼스트 네임 순으로 다시 한번 정렬해서 출력한다

---

### lecture 128 - Using LIMIT

자료 갯수를 제한할 때 LIMIT을 사용할 수 있다
```sql
SELECT title FROM books LIMIT 3;
```
* books테이블에 입력된 제목 3개만 가져온다

출간일 역순으로 제목과 출간일 데이터를 5개만 가져오기
```sql
SELECT title, released_year FROM books ORDER BY released_year DESC LIMIT 5;
```

콤마로 더 명확하게 갯수 제한 쿼리문을 작성할 수 있다
```sql
SELECT title, released_year FROM books ORDER BY released_year DESC LIMIT 0, 5;
```
* 첫 번째 숫자는 시작점이고, 두 번째 숫자는 가져올 데이터의 갯수를 의미한다

---

### lecture 130 Better Searched with LIKE

자료를 검색해야 하는데 명확한 이름이 기억나지 않을 때 LIKE를 사용하면 유용하다

퍼스트 네임에 da가 들어가는 작가의 퍼스트 네임과 책 제목을 출력하기
```sql
SELECT title, author_fname FROM books WHERE author_fname LIKE '%da%';
```

da로 시작하는 자료만 찾고 싶다면 앞쪽의 와일드 카드(% - percent sign)를 빼면된다
```sql
SELECT title, author_fname FROM books WHERE author_fname LIKE 'da%';
```

---

### lecture 132 LIKE Part 2 : More Wildcards

언더스코어를 이용해 문자열의 갯수를 검색할 수 있다
```sql
SELECT title, stock_quantity FROM books WHERE stock_quantity LIKE '____'(언더스코어 4번);
```
* stock_quantity가 네 자리인 자료의 제목과 재고량을 가져온다

LIKE에 %나 _를 사용해서 패턴매칭을 하고싶다면
```sql
LIKE '%\%%';
LIKE '%\_%';
```
* 위와 같이 백스페이스를 넣어서 사용할 수 있다

---

### lecture 134 Refining Selection Exercise

stories라는 문자열을 타이틀에 포함한 자료를 가져오기
```sql
SELECT title FROM books WHERE title LIKE '%stories%';
```

가장 페이지가 많은 책 하나를 가져오기
```sql
SELECT title, pages FROM books ORDER BY pages DESC LIMIT 1; 
```

가장 최근에 출간된 책 3권을 summary라는 이름으로 제목 - 출간년 형식으로 가져오기
```sql
SELECT CONCAT(title, ' - ' , released_year) AS summary FROM books 
ORDER BY released_year DESC LIMIT 3; 
```

라스트 네임에 공백이 들어간 작가의 모든 책을 제목, 라스트 네임만 가져오기
```sql
SELECT title, author_lname FROM books WHERE author_lname LIKE '% %';
```

재고량이 가장 적은 책 3개의 제목, 출간년, 재고량을 가져오기
```sql
SELECT title, released_year, stock_quantity FROM books ORDER BY stock_quantity LIMIT 3; 
```

제목과 저자의 라스트네임을 출력하기 - 라스트네임 순으로 먼저 정렬한 뒤 타이틀 순으로 정렬하기
```sql
SELECT title, author_lname FROM books ORDER BY author_lname, title;
SELECT title, author_lname FROM books ORDER BY 2, 1;
```

라스트네임 순으로 정렬한 작가의 풀네임을 yell이라는 이름으로 MY FAVORITE AUTHOR IS FULL NAME! 형식으로 가져오기
```sql
SELECT UPPER(CONCAT('MY FAVORITE AUTHOR IS ', author_fname, ' ', author_lname, '!')) AS YELL 
FROM books ORDER BY author_lname;
```