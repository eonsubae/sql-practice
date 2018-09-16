# Section 2

### lecture 9. What is a Database?

두 가지 질문을 다룬다
1. 데이터베이스란 무엇인가
2. 데이터베이스가 왜 중요한가

데이터베이스란 무엇인가
* 두 가지 정의
  1. 데이터 집합(할일 목록, 병원 차트, 식료품점 상품 목록)
  2. 데이터에 접근하고 조작하기 위한 메서드

예제
```txt
Andrew, Archie - (949) 354 - 2222
Cooper, Betty - (212) 246 - 9846
Flanders, Ned - (415) 987 - 3451
```
* 전화번호부 목록의 일부 섹션이다
* 데이터베이스를 이용하면 이렇게 데이터 집합을 규칙적으로 다룰 수 있다
* 일정한 규칙대로 쓰여져 있기 때문에 엄청난 두께의 책임에도 불구하고 쉽게 번호를 찾을 수 있다
* 또한 전화번호를 추가, 조회, 수정, 삭제하면서 조작할 수 있다

Database vs Database Management System
* 데이터베이스에 대한 포스트들을 보면 많은 전문용어가 나온다
  - Database Management System
  - relational database management system
  - DMX
  - RDBMS
* 데이터베이스는 수많은 저장된 데이터의 집합을 의미한다
  - 그러나 여러 데이터를 저장하는 것만으로는 데이터베이스를 통해 얻으려는 기능을 충분히 제공받지 못한다
* DBMS
  - DBMS는 여러 데이터에 접근해서 데이터를 조작할 수 있는 인터페이스를 제공한다

결론
* 데이터베이스는 계산할 수 있는 데이터에 접근 가능한 인터페이스를 가진 구조화된 집합이다

---

### lecture 10. SQL vs MySQL

SQL이란
* Structured Query Language
* SQL은 우리가 사용하는 데이터베이스에 말을 걸 수 있는 언어다

18살 이상인 모든 사용자를 찾는 예제
```sql
SELECT * FROM Users WHERE Age >= 18;
```

MySQL을 포함한 여러 데이터베이스들의 공통점
* SQL을 사용한다
* 관계형 데이터베이스다
* 여러 데이터베이스들은 아주 약간씩 문법이 다르다

---

### lecture 17. CODE: Installing MySQL on Cloud9

mysql 시작
* mysql-ctl start
 
cli 모드로 진입
* mysql-ctl cli
 
mysql 종료
* mysql-ctl stop
 
cli 모드에서 빠져나오기
* exit;
* quit;
* \q;
* ctrl-c

---

### lecture 18. Your First MySQL Activity

help; 를 입력해보기
* 사용할 수 있는 cli 목록과 설명이 나온다
* 서버와 관련된 내용들을 알고 싶다면 help contents를 입력하면 목록이 나온다
  - ex. help data types; 를 입력하면 사용 가능한 데이터 타입 목록을 보여준다

show databases; 를 입력해보기
* 현재 인스턴스에서 사용가능한 데이터베이스 목록을 보여준다

select @@hostname; 을 입력해보기
* 유저 이름과 워크 스페이스 이름을 보여준다

---