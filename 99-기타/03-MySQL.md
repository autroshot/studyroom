# MySQL

최신 버전은 오류가 발생하므로 8.0.22.0 버전으로 설치한다.

Node.js에서 DB 접속에 문제가 있으면 `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '1234'`를 적용한다.

## mysql2

Node.js의 패키지로 DB에 접속할 있게 해준다.

SQL 주입 공격을 막기 위해 Prepared Statement를 사용해야 한다.