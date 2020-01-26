# 설치 관련 정리

Oracle 11g Express 버전 설치
* https://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html
* 이후 라이선스 동의를 하고 각자의 PC사양에 맞는 버전을 내려받으면 된다
* 내려받기 위해 다운로드 버튼을 클릭하면 로그인 창이 나타난다
* (계정이 없으면 가입한 후) 로그인하면 자동으로 다운로드가 시작된다
* 파일을 내려 받으면 해당 파일(ex. OracleXE112_Win64.zip)의 압축을 해제한다
* 압축을 푼 후 해당 폴더에서 setup.exe를 실행한다
* 경로 설치와 SYSTEM 계정 패스워드를 각자 상황에 맞게 지정한다
* Oracle최초 사용시 SYSTEM계정으로 로그인 해야 하므로 패스워드를 반드시 기억해둬야 한다

Oracle 11g Expres 버전 환경설정
* 설치 이후 사용을 위해 몇 가지 처리해야 할 작업이 있다

사용자 생성
```terminal
SQL> CREATE USER <your-name> IDENTIFIED BY <your-password> DEFAULT TABLESPACE USERS;
```
* 사용자 아이디와 비밀번호를 원하는 대로 각각 입력하자
* default이하 구문은 이 사용자는 기본적으로 USERS란 테이블 스페이스을 사용한다는 의미다
* 따라서 이 아이디로 로그인해서 테이블을 생성하면 USERS 테이블 스페이스에 생성된다

사용자 권한 할당
```terminal
SQL> GRANT DBA TO <your-name>;
```
* 연습시에는 위와 같이 DBA권한을 주면 거의 대부분의 작업이 수행가능하다

로그인하기
```terminal
SQL> conn <your-name>/<your-password>;
Connected.
SQL> SELECT USER FROM DUAL;
```
