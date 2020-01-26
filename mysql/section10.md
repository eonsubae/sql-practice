# Section 10 - Revisiting Data Types

### lecture 156 - CHAR and VARCHAR

텍스트 저장하기
* CHAR와 VARCHAR의 차이점
  - CHAR는 테이블을 처음 만들 때 선언했던 길이로 length가 고정되어 있다
  - length는 0에서 255까지 가능하다
  - right-padded : 10으로 length를 지정하고 5개의 문자를 입력했다면 오른쪽에 빈 공간을 스페이스로 채운다
  - length를 넘어서 입력한 문자는 잘린다(truncate)
    - 강의에서는 INSERT문으로 지정된 length보다 더 많은 문자를 입력했을 때 잘려서 저장된다
    - 그러나 최신 mysql에서는 데이터가 너무 길게 입력되었다는 오류가 발생하며 저장되지 않는다 
  - CHAR는 길이가 고정된 문자를 입력받는 상황에 유용하다. 길이가 고정되어 있어 성능이 빠르다
  - ex)주 이름:NY, CA, 예스/노 : Y/N, 성별:M/F 등..
  - 이렇게 고정된 문자를 필요로하는 상황 외에는 VARCHAR가 보다 유연하므로 VARCHAR를 많이 사용한다
  - VARCHAR도 길이를 지정하는 것은 같으나 저장된 값에 따라 차지하는 메모리가 달라지기 때문이다(CHAR는 고정 4바이트)

---

### lecture 159 - DECIMAL

DECIMAL
```sql
DECIMAL(5, 2);
```
* DECIMAL은 인자를 2개 받는다
  - 첫번째 인자는 정수부와 실수부를 합친 총자리수를 의미한다(ex. 999.99 - 5자리)
  - 두 번째 인자는 실수부 자리수를 의미한다(ex. 999.99에서 .99)
  - 일반화해서 DECIMAL(M, D)로 나타냈을 때, M은 1에서 65까지의 범위를 가지며, D는 0에서 30까지 범위를 가진다
    - 단, D가 M보다 길어서는 안된다
  - 강의에서는 인자보다 큰 수를 입력하면 999.99처럼 표현할 수 있는 가장 큰 숫자로 변환되어 저장된다
    - 그러나 최신버전에서는 입력된 숫자가 너무 크다는 에러 메시지가 반환되며 저장되지 않는다

---

### lecture 161 - FLOAT and DOUBLE

DECIMAL과 FLOAT, DOUBLE의 차이점
* DECIMAL은 고정 소수점 방식을 사용한다
* 반대로 FLOAT과 DOUBLE은 부동 소수점 방식을 사용한다
* 고정 소수점과 부동 소수점 방식은 trade-off하다
  - 고정 소수점은 정확도는 높으나 긴 숫자를 표현하기 어렵다
  - 반대로 부동 소수점은 정확도는 낮으나 긴 숫자를 표현하기 쉽다

FLOAT과 DOUBLE
* FLOAT은 4바이트를 필요로하며 자리수가 7이상이면 정확도 이슈가 생긴다
* DOUBLE은 8바이트를 필요로하며 자리수가 15이상이면 정확도 이슈가 생긴다

---

### lecture 164 - DATE, TIME, AND DATETIME

DATE
* 시간 없이 날짜만 저장하는 데이터 타입
* 'YYYY-MM-DD'와 같은 형식을 사용한다

TIME
* 날짜 없이 시간만 저장하는 데이터 타입
* 'HH:MM:SS'와 같은 형식을 사용한다

DATETIME
* 날짜와 시간을 같이 저장하는 데이터 타입
* 'YYYY-MM-DD HH:MM:SS'와 같은 형식을 사용한다
* 세 가지 데이터 타입 중에서 일반적으로 가장 많이 사용된다

---

### lecture 167 - CURDATE, CURTIME, AND NOW

각각 현재 날짜, 현재 시간, 현재 날짜와 시간을 저장하는 함수다

다음과 같이 사용할 수 있다
```sql
CREATE TABLE people (
  name VARCHAR(100),
  birthdate DATE,
  birthtime TIME,
  birthdt DATETIME
);

INSERT INTO people (name, birthdate, birthtime, birthdt) VALUES ('Toaster', CURDATE(), CURTIME(), NOW());
```

---

### lecture 169 - Formatting Dates

mysql에는 날짜를 표기하는 방법을 변형시켜주는 함수들이 있다

DAY
* 날짜 중 일(년-월-일에서의 일)에 해당되는 값만 추출하는 함수
```sql
SELECT name, birthdate, DAY(birthdate) FROM people;
```
* 원래 birthdate는 'YYYY-MM-DD' 형식이지만 DAY 함수를 적용하면 DD값만 추출된다

DAYNAME
* 날짜를 요일로 바꿔준다(Monday, Friday, etc...)
```sql
SELECT name, birthdate, DAYNAME(birthdate) FROM people;
```

DAYOFWEEK
* 날짜를 숫자로 바꿔준다

DAYOFYEAR
* 해당 년도에서 몇번째 날인지를 숫자로 나타내준다
```sql
SELECT name, birthdt, DAYOFYEAR(birthdt) FROM people;
```

MONTH
* 몇 월인지를 숫자로 나타내준다

MONTHNAME
* 몇 월인지를 문자로 나타내준다(April, ...)

함수들을 이용해서 '월(문자로) 일 년' 형식으로 날짜 나타내기
```sql
SELECT CONCAT(MONTHNAME(birthdate), ' ', DAY(birthdate), ' ', YEAR(birthdate)) FROM people;
```

DATE_FORMAT
* 앞서 사용했던 여러 함수들의 기능을 %문자 형식의 인자를 추가하는 것으로 같은 기능을 하게 만들어주는 함수
```sql
SELECT DATE_FORMAT('2009-10-24 22:23:00', '%W %M %Y');
```
* Saturday October 2009가 출력된다

```sql
SELECT DATE_FORMAT(birthdt, 'Was Born On A %W') from people;
SELECT DATE_FORMAT(birthdt, '%m/%d/%y') from people;
```

---

### lecture 171 - Date Math

시간을 가지고 수학적 계산을 처리하는 함수들을 다룬다

DATEDIFF
* 인자로 보낸 두 개의 일(day)의 차를 반환한다
```sql
SELECT DATEDIFF(NOW(), '2019-02-22');
```
* 이 문서를 작성한 날은 2019-02-23이므로 1이 반환된다

```sql
SELECT DATEDIFF(NOW(), '2019-03-22');
```
* -27이 반환된다(2월은 28일까지임을 유의해서 계산하자)

DATE_ADD
* INTERVAL로 지정한 값을 더한다
```sql
SELECT birthdt, DATE_ADD(birthdt, INTERVAL 1 MONTH) FROM people;
SELECT birthdt, DATE_ADD(birthdt, INTERVAL 10 SECOND) FROM people;
SELECT birthdt, DATE_ADD(birthdt, INTERVAL 3 QUARTER) FROM people;
```

DATE_SUB
* DATE_ADD의 반대로 동작한다

+/- 연산자로 같은 기능 수행하기
```sql
SELECT birthdt, birthdt + INTERVAL 1 MONTH FROM people;
SELECT birthdt, birthdt + INTERVAL 10 SECOND FROM people;
SELECT birthdt, birthdt + INTERVAL 3 QUARTER FROM people;
```
* 위의 DATE_ADD 함수에서 작성했던 예제는 + 연산자를 이용해 위와 같이 사용할 수도 있다

---

### lecture 173 - Working with TIMESTAMPS

날짜를 저장하는 공간(메모리)을 절약하기 위해 사용하는 방법

예제를 위한 테이블 생성
```sql
CREATE TABLE comments (
  content VARCHAR(100),
  created_at TIMESTAMP DEFAULT NOW() 
);
```

더미 데이터 생성
```sql
INSERT INTO comments (content) VALUES ('lol what a funny article');
INSERT INTO comments (content) VALUES ('I found this offensive'); 
```

변경시 변경된 시각을 저장하게끔 테이블을 만들기
```sql
CREATE TABLE comments2 (
  content VARCHAR(100),
  changed_at TIMESTAMP DEFAULT NOW() ON UPDATE CURRENT_TIMESTAMP
);
```
* 데이터가 변경될 시(ON UPDATE)
* 현재 시각(CURRENT_TIMESTAMP)을 저장한다

데이터를 집어넣고 변경해보자
```sql
INSERT INTO comments2 (content) VALUES ('HI, THERE');
SELECT * FROM comments2;
UPDATE comments2 SET content='Hi, There' WHERE content='HI, THERE';
SELECT * FROM comments2;
```

---

### lecture 175 - Data Types Exercises

CHAR를 사용하기 좋은 케이스는?
* 내 대답 : 길이가 고정된 문자열을 나타낼 때 유용하다(ex. Y/N)
* Used for text that we know has a fixed length, e.g., State abbreviations, abbreviated company names, sex M/F, etc.

빈 칸을 알맞게 채우기
```sql
CREATE TABLE inventory (
  item_name ____,
  price ____,
  quantity ____,
);
```
* 내 대답 
  ```sql
  CREATE TABLE inventory (
    item_name VARCHAR(100),
    price INT,
    quantity INT,
  );
  ```
* 강의자
  ```sql
  CREATE TABLE inventory (
    item_name VARCHAR(100),
    price DECIMAL(8,2),
    quantity INT
  );
  ```

DATETIME과 TIMESTAMP의 차이점은?
* They both store datetime information, but there's a difference in the range, 
TIMESTAMP has a smaller range. TIMESTAMP also takes up less space. 
TIMESTAMP is used for things like meta-data about when something is created
or updated.

현재 시간을 출력하기
```sql
SELECT CURTIME();
```

현재 날짜만 출력하기
```sql
SELECT CURDATE();
```

이번주에서 현재 날짜를 숫자로 출력하기
```sql
SELECT DAYOFWEEK(CURDATE());
SELECT DAYOFWEEK(NOW());
SELECT DATE_FORAMT(NOW(), '%w') + 1;
```

이번주에서 현재 날짜를 요일(문자)로 출력하기
```sql
SELECT DAYNAME(NOW());
SELECT DATE_FORMAT(NOW(), '%W');
```

MM/DD/YYYY 포맷으로 현재 날짜를 출력하기
```sql
SELECT DATE_FORMAT(NOW(), '%m/%d/%Y');
```

'월 날짜(ex. 1일은 - 1st, 2일은 - 2rd) at 시:분' 형식으로 출력하기
```sql
SELECT DATE_FORMAT(NOW(), '%M %D at %h:%m');
SELECT DATE_FORMAT(NOW(), '%M %D at %h:%i');
```

트위터 테이블 만들기
* 저장해야할 자료는 다음과 같다
  - 트윗 콘텐츠
  - 유저네임
  - 생성시각
```sql
CREATE TABLE tweets (
  content VARCHAR(140),
  username VARCHAR(20),
  created_at TIMESTAMP DEFAULT NOW()
);
```