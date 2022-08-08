# MySQL

최신 버전은 오류가 발생하므로 8.0.22.0 버전으로 설치한다.

서버가 작동하지 않는다면 서비스에 MySQL80이 실행 중인지 확인한다.

Node.js에서 DB 접속에 문제가 있으면 `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '1234'`를 적용한다.

## mysql2

Node.js의 패키지로 DB에 접속할 있게 해준다.

SQL 주입 공격을 막기 위해 Prepared Statement를 사용해야 한다.

SELECT  query(execute)의 결과는 `[RowDataPacket[], FieldPacket[]]`이며 `RowDataPacket`을 상속한 인터페이스로 타입을 지정할 수 있다.

INSERT query의 결과는 `[ResultSetHeader, FieldPacket[]]`이며 id는 `ResultSetHeader.insertId`로 얻을 수 있다.

## 명명 규칙

- snake_case를 사용한다.
- 접두사나 접미사는 사용하지 않는다.
- 테이블명은 복수가 아닌 단수를 사용한다.
- 약어는 사용하지 않는다.
- 인조 식별자는 `id`로 짓는다.
- 외래키는 `user_id`와 같이 짓는다.
- 인덱스와 제약 조건은 서술적으로 짓는다.

