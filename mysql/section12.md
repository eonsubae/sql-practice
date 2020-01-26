# Section 12. One To Many

### lecture 201. Real World Data is Messy

지금까지 다룬 것
* 격리된 하나의 테이블 안에서 특정한 조작을 가하는 것(ex. books 테이블)

현실의 데이터
* 하나의 데이터가 다른 데이터들과 복잡하게 연결되어 있다(ex. books, customers, reviews, genres, orders, authors, versions, ...)
* 따라서 현실의 애플리케이션을 잘 만들기 위해서는 데이터들의 관계를 잘 다뤄야 한다

---

### lecture 202. Types of Data Relationships

데이터 관계들
* One to One(1 : 1 관계)
  - ex. books - customer
* One to Many(1 : N 관계)
  - ex. books - reviews
* Many to Many(M : N 관계)
  - ex. books - authors

---

### lecture 203. One To Many : The Basics

Customers & Orders
* 한 명의 손님은 여러 개의 주문을 가진다
* 반대로 한 주문이 여러 명의 손님에 의해 이루어지지는 않는다
* 따라서 이 관계는 1 : Many(One To Many)관계라고 할 수 있다

요구사항
* Customer의 퍼스트 네임과 라스트 네임(first_name, last_name)
* Customer의 이메일(email)
* 주문일(order_date)
* 주문 총액(amount)

어떻게 구현해야 할까
* 하나의 테이블에 모든 정보를 넣기
  - Customer가 위에서 작성한 모든 요구사항에 해당하는 컬럼을 가질 수도 있다
  - 그러나 유저 정보만 가져오고 싶을 때 혹은 주문 정보만 가져오고 싶을 때 필요없는 정보를 추가로 가져와야 하는 문제가 있다
  - 따라서 Customers 테이블과 Orders 테이블을 나누고 둘을 관계 맺어주면 불필요한 중복이 사라진다
* 테이블 나누기
  - Customers : customer_id, first_name, last_name, email
  - Orders : order_id, order_date, amount, customer_id

예시

Customers
 customer_id | first_name | last_name | email 
-------------|------------|-----------|------------------
      1      |     Boy    |  George   | george@gmail.com
      2      |     George |  Michael  | gm@gmail.com
      3      |     David  |  Bowie    | david@gmail.com
      4      |     Blue   |  Steele   | blue@gmail.com

Orders
 order_id |  order_date  |  amount  | customer_id
----------|--------------|----------|--------------
    1     | '2016/02/10' |   99.99  |       1
    2     | '2017/11/11' |   35.50  |       1
    3     | '2014/12/12' |   800.67 |       2
    4     | '2015/01/03' |   12.50  |       2

One To Many관계를 맺기 위해 알아야할 개념
* PRIMARY KEY
  - 앞서 데이터 구분을 위해 특정 컬럼을 만들 때 INT AUTO_INCREMENT PRIMARY KEY를 사용한 바 있다
  - PRIMARY KEY가 의미하는 것은 해당 컬럼은 항상 유니크한 컬럼이라는 의미를 가지고 있다
* Foreign Key
  - 외부 테이블의 컬럼을 참조하는 컬럼에 지정한다

---

### lecture 204. Working With Foreign Keys

앞서 배웠던 개념들과 자료들을 이용해서 실제로 코딩해보기

customers 테이블 만들기
```sql
CREATE TABLE customers(
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    email VARCHAR(100)
);
```

orders 테이블 만들기
```sql
CREATE TABLE orders(
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_date DATE,
    amount DECIMAL(8,2),
    customer_id INT,
    FOREIGN KEY(customer_id) REFERENCES customers(id)
);
```
* FOREIGN KEY와 REFERENCES 라는 예약어로 1:N 관계를 맺고 있다

실제 데이터 집어 넣기
```sql
INSERT INTO customers (first_name, last_name, email) 
VALUES ('Boy', 'George', 'george@gmail.com'),
       ('George', 'Michael', 'gm@gmail.com'),
       ('David', 'Bowie', 'david@gmail.com'),
       ('Blue', 'Steele', 'blue@gmail.com'),
       ('Bette', 'Davis', 'bette@aol.com');
       
INSERT INTO orders (order_date, amount, customer_id)
VALUES ('2016/02/10', 99.99, 1),
       ('2017/11/11', 35.50, 1),
       ('2014/12/12', 800.67, 2),
       ('2015/01/03', 12.50, 2),
       ('1999/04/11', 450.25, 5);
```

외래 키(Foreign Key)를 지정하지 않았을 때 문제점
* customers의 id가 없는 경우에도 orders의 customer_id에 값이 들어갔을 때 에러가 발생하지 않는다
* 위와 같이 FK를 지정하면 존재하지 않는 customers의 id를 입력했을 때 에러를 발생시킨다
```sql
INSERT INTO orders (order_date, amount, customer_id)
VALUES ('2016/06/06', 33.67, 98);
```

---

### lecture 206. Cross Join

앞서 만든 데이터의 관계를 통해 데이터를 추출해보기

서브쿼리를 이용해 George라는 라스트 네임을 가진 손님의 주문 정보를 추출하기
```sql
SELECT * FROM orders WHERE customer_id = 
(
  SELECT id FROM customers WHERE last_name='George'
);
```

기본 조인(basic join)을 이용해 두 개의 테이블을 결합하기
* 참고 : 실제로는 많이 사용하지 않지만 알아두면 좋기 때문에 설명한 것이다
* cartesian join이라고도 불린다
```sql
SELECT * FROM customers, orders;
```
* 모든 customers들이 모든 orders들과 결합된다
  - 따라서 연산이 custmers의 갯수 x orders의 갯수만큼 이루어진다
  - 이처럼 비효율적이기 때문에 잘 사용하지 않는다

---

### lecture 208. Inner join

앞서 사용했던 카테시안 조인의 비효율성을 어떻게 개선할 수 있을까?

조건절을 추가하기
```sql
SELECT * FROM customers, orders WHERE id = customer_id;
```
* customers, orders 모두에 id컬럼이 있기 때문에 에러가 발생한다

테이블 명을 지정해 조건절을 추가하기
```sql
SELECT first_name, last_name, order_date, amount
FROM customers, orders 
WHERE customers.id = orders.customer_id;
```
* 두 가지 테이블을 가지고 쿼리문을 작성할 때는 컬럼 앞에 테이블 명을 붙여주는 것이 좋다
  - 문법 오류를 방지하는 효과
  - 명확하게 어떤 컬럼이 어떤 테이블에 있는 것인지 확인할 수 있다
* 이렇게 복수의 테이블에서 겹치는 부분(overlap)만 추출해 내는 조인을 Inner join이라고 한다
  - 그런데 sql문법에는 Inner join을 위한 JOIN과 ON 키워드가 있다
  - 위에서 작성한 쿼리문처럼 전용 키워드를 사용하지 않는 Inner join을 Implicit Inner join이라 한다

JOIN과 ON을 이용한 Inner join
```sql
SELECT first_name, last_name, order_date, amount FROM customers
JOIN orders
ON customers.id = orders.customer_id;
```
* 이처럼 명시적으로 조인을 위한 문법을 사용한 것은 Explicit Inner join이라 한다
* 이처럼 조인을 사용할 때는 명시적으로 조인과 관련된 문법을 사용해서 작성하는 것이 좋다

조인의 다양성
* 지금까지 배운 카테시안 조인, 이너 조인 외에도 레프트 조인, 라이트 조인 등 다양한 종류가 있다
* 이런 개념들에 압도당하지 말고 조인은 어디까지나 데이터들을 조건에 맞게 결합시키는 것이라는 본질에 집중하자
* 예를 들어 다음과 같은 조인문은 쓸모없는 행위다
```sql
SELECT * FROM customers
JOIN orders
ON customers.id = orders.id;
```
* customers의 id와 orders의 id가 동시에 필요한 경우는 아마 발생하지 않을 것이다
* 이처럼 조건에 맞게 데이터를 적절히 결합하는 것이 조인의 핵심이다

테이블의 순서를 바꾸면 일어나는 일들
```sql
SELECT first_name, last_name, order_date, amount FROM orders
JOIN customers
ON customers.id = orders.customer_id;
```
* orders와 customers의 위치를 스위칭했으나 결과는 똑같다
* 이는 출력되는 컬럼의 순서를 SELECT 뒤에 명시적으로 지정했기 때문이다

명시적으로 출력되는 컬럼을 지정하지 않을 때
```sql
SELECT * FROM orders
JOIN customers
ON customers.id = orders.customer_id;
```
* orders의 컬럼들부터 데이터가 출력되는 것을 확인할 수 있다

---

### lecture 210. Left join

데이터를 병합하고 원하는 결과를 얻기 위해 추가적인 조작을 가할 수 있다

가장 많이 구매한 구매자를 계산하기 위한 자료를 추출하기
```sql
SELECT first_name, last_name, SUM(amount) AS total_spent
FROM customers
JOIN orders
ON customers.id = orders.customer_id
GROUP BY orders.customer_id
ORDER BY total_spent DESC;
```

Left Join
* 앞서 살펴본 Inner Join이 A, B 테이블의 교집합에 속하는 데이터였다면
* Left Join은 A 전체의 데이터를 가져온다
  - 위의 예시로 설명하자면 customers 전체의 데이터(orders와 연관된 데이터 포함)를 가져오는 것이다

Inner Join과 Left Join 문법
```sql
-- INNER JOIN
SELECT * FROM customers
INNER JOIN orders
ON customers.id = orders.customer_id;
```
```sql
-- LEFT JOIN
SELECT * FROM customers
LEFT JOIN orders
ON customers.id = orders.customer_id;
```
* 둘의 결과를 출력해보고 비교해보자
* INNER JOIN은 orders와 연관된 데이터만을 가져온다
* LEFT JOIN은 orders와 연관된 데이터와 그 외의 customers의 데이터를 함께 가져온다

언제 Left Join을 사용할까?
* 앞서 가장 많이 소비한 유저를 찾은 바 있다
* 반대로 한 번도 소비하지 않은 유저들을 찾아야 하는 요구사항이 있을 수도 있다
* 한 번도 구매하지 않은 유저를 찾아내 첫 구매를 위한 쿠폰을 제공하는 등의 이벤트를 기획할 수도 있기 때문이다
* 이처럼 특정 조건에 매칭되지 않는 유저를 찾아낼 때 Left Join이 유용하게 사용될 수 있다
```sql
SELECT 
  first_name, 
  last_name, 
  IFNULL(SUM(amount), 0) AS total_spent
FROM customers
LEFT JOIN orders
ON customers.id = orders.customer_id
GROUP BY customers.id
ORDER BY total_spent;
```
* IFNULL은 첫번째 인자의 값이 NULL이면 두 번째 인자의 값으로 변경해주는 함수다

---

### lecture 212. Right Joins Pt 1

Right Join은 Left Join과 반대로 어떤 테이블 A와 연관된 테이블 B 전체의 데이터를 가져온다
```sql
SELECT * FROM customers
RIGHT JOIN orders
ON customers.id = orders.customer_id;
```

---

### lecture 214. Right Joins Pt 2

주문 내역은 남아있지만 유저 정보가 삭제되었을 때
```sql
SELECT 
    IFNULL(first_name,'MISSING') AS first, 
    IFNULL(last_name,'USER') AS last, 
    order_date, 
    amount, 
    SUM(amount)
FROM customers
RIGHT JOIN orders
    ON customers.id = orders.customer_id
GROUP BY first_name, last_name;
```
* 삭제된 유저들이 구매한 액수의 총합을 구할 수 있다

외래 키로 연결된 특정 자료가 삭제될 때 연결된 다른 테이블의 자료도 삭제되도록 테이블 만들기
```sql
CREATE TABLE orders(
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_date DATE,
    amount DECIMAL(8,2),
    customer_id INT,
    FOREIGN KEY(customer_id) REFERENCES customers(id)
    ON DELETE CASCADE 
);
```

---

다음과 같은 스키마를 입력하기
* students
  - id
  - first_name
* papers
  - title
  - grade
  - student_id(fk)

```sql
CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(100)
);

CREATE TABLE papers (
    title VARCHAR(100),
    grade INT,
    student_id INT,
    FOREIGN KEY(student_id) REFERENCES students(id) ON DELETE CASCADE
);
```

입력할 연습용 데이터
```sql
INSERT INTO students (first_name) VALUES 
('Caleb'), 
('Samantha'), 
('Raj'), 
('Carlos'), 
('Lisa');
 
INSERT INTO papers (student_id, title, grade ) VALUES
(1, 'My First Book Report', 60),
(1, 'My Second Book Report', 75),
(2, 'Russian Lit Through The Ages', 94),
(2, 'De Montaigne and The Art of The Essay', 98),
(4, 'Borges and Magical Realism', 89);
```

입력된 모든 학생의 first_name, title, grade를 출력
```sql
SELECT first_name, title, grade FROM students
INNER JOIN papers
ON students.id = papers.student_id
ORDER BY grade DESC;
```

paper 정보가 없는 Raj와 Lisa의 title과 grade를 NULL로 출력
```sql
SELECT first_name, title, grade FROM students
LEFT JOIN papers
ON students.id = papers.student_id;
```

모든 학생들의 평균 점수를 구하기
```sql
SELECT first_name, IFNULL(AVG(grade), 0) AS average FROM students
LEFT JOIN papers
ON students.id = papers.student_id
GROUP BY students.id
ORDER BY average DESC;
```

passing_status컬럼에 평균 점수가 75점 이상이면 PASSING을 미만이면 FAILING을 출력하기
```sql
SELECT 
  first_name, 
  IFNULL(AVG(grade), 0) AS average,  
  CASE
    WHEN IFNULL(AVG(grade), 0) >= 75 THEN 'PASSING'
    ELSE 'FALING'
  END AS 'passing_status'
FROM students
LEFT JOIN papers
ON students.id = papers.student_id
GROUP BY first_name
ORDER BY grade DESC;
```