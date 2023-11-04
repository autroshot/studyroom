# MySQL

가장 많이 사용되는 RDBMS 중 하나이다. 오픈 소스이다.

MySQL은 키워드와 구문에서 대소문자를 구분하지 않는다. 하지만 테이블과 필드의 이름에서는 구분한다.

주석은 작성하는 방법은 다음과 같다.

```mysql
# 한 줄 주석
-- 한 줄 주석
/* 
여러 줄
주석
*/
```

## 명령어

### `CREATE`

데이터베이스 만들기

```mysql
CREATE DATABASE Hotel;
```

만들어진 데이터베이스는 다음 명령을 통해 사용할 수 있다.

```mysql
USE Hotel;
```

테이블 만들기

```mysql
CREATE TABLE Reservation
(
    ID INT, 
    Name VARCHAR(30), 
    ReserveDate DATE, 
    RoomNum INT
);
```

테이블을 만들 때 설정할 수 있는 제약 조건은 다음과 같다.

| 제약 조건        | 설명                                                         |
| ---------------- | ------------------------------------------------------------ |
| `NOT NULL`       | null 값을 지정할 수 없음                                     |
| `UNIQUE`         | 서로 다른 값을 가져야 함                                     |
| `PRIMARY KEY`    | 기본 키. `NOT NULL`과 `UNIQUE`를 모두 가짐.                  |
| `FOREIGN KEY`    | 외래 키                                                      |
| `DEFAULT`        | 기본값을 설정함                                              |
| `AUTO_INCREMENT` | 1부터 자동으로 증가하는 값을 저장. `=`로 시작값을 지정 가능. |

### `ALTER`

[링크](https://www.tcpschool.com/mysql/mysql_basic_alter) 참고

### `DROP`

데이터베이스 삭제

```mysql
DROP DATABASE Hotel;
```

테이블 삭제

```mysql
DROP TABLE Reservation;
```

테이블의 데이터만을 지우고 싶다면 다음 명령을 사용한다.

```mysql
TRUNCATE TABLE Reservation;
```

### `INSERT`

```mysql
INSERT INTO Reservation(ID, Name, ReserveDate, RoomNum) 
VALUES(5, '이순신', '2016-02-16', 1108);

INSERT INTO Reservation 
VALUES(5, '이순신', '2016-02-16', 1108);
```

### `UPDATE`

```mysql
UPDATE Reservation 
SET RoomNum = 2002 
WHERE Name = '홍길동';
```

### `DELETE`

```mysql
DELETE FROM Reservation 
WHERE Name = '홍길동';
```

### `SELECT`

```mysql
# 모든 필드 선택
SELECT * FROM Reservation;

# 조건 넣기
SELECT * FROM Reservation 
WHERE 
    Name = '홍길동' AND 
    ReserveDate = '2023-11-04';
    
# 아래 두 명령은 동일한 작업을 한다.
SELECT * FROM Reservation 
WHERE RoomNum BETWEEN 201 AND 250;
SELECT * FROM Reservation 
WHERE RoomNum >= 201 AND RoomNum <= 250;
    
# 중복되는 값 제거
SELECT DISTINCT Name FROM Reservation;

# 정렬
SELECT * FROM Reservation ORDER BY ReserveDate;
SELECT * FROM Reservation ORDER BY ReserveDate DESC;
SELECT * FROM Reservation 
ORDER BY ReserveDate DESC, RoomNum ASC;
    
# 별칭
SELECT ReserveDate, CONCAT(RoomNum, " : ", Name) AS ReserveInfo 
    FROM Reservation;
    
# 전체 예시
SELECT 
[DISTINCT]
    select_expr [, select_expr] ...
[FROM table_references]
[JOIN table_references]
    [ON on_condition]
[WHERE where_condition]
[GROUP BY {col_name | expr | position}, ...]
[HAVING where_condition]
[ORDER BY {col_name | expr | position} [ASC | DESC], ...]
[LIMIT {[offset,] row_count | row_count OFFSET offset}]
```

## 타입

### 숫자

정수형은 `UNSIGNED`을 붙여 양수만 사용하도록 지정할 수 있다. 음수 범위가 없어지는 만큼 양수 범위가 늘어난다.

다음은 모든 정수 타입이다.

| 타입        | 저장 공간 (Bytes) | `SIGNED`                 | `UNSIGNED`     |
| :---------- | :---------------- | :----------------------- | :------------- |
| `TINYINT`   | 1                 | -128 ~ 127               | 0 ~ 255        |
| `SMALLINT`  | 2                 | -32768 ~ 32767           | 0 ~ 65535      |
| `MEDIUMINT` | 3                 | -8388608 ~ 8388607       | 0 ~ 16777215   |
| `INT`       | 4                 | -2147483648 ~ 2147483647 | 0 ~ 4294967295 |
| `BIGINT`    | 8                 | $-2^{63}$ ~ $2^{63}-1$   | 0 ~ $2^{64}-1$ |

`DECIMAL(M,D)`은 고정 소수점 타입이다.

부동 소수점 타입에는 `FLOAT(P)`과 `DOUBLE(P)`이 있다.

### 불

MySQL에서는 불 타입을 원시적으로 지원하지 않으나 `TINYINT` 타입을 사용하여 `BOOLEAN`을 지원한다. `TRUE`는 1로, `FALSE`는 0으로 저장된다.

`WHERE`에서는 `IS TRUE`와 `IS FALSE`를 사용할 수 있다.

### 문자열

- `VARCHAR(M)`: 가변 문자열
- `BLOB`: 바이너리 데이터
- `ENUM('데이터값1', '데이터값2', ...)`: 열거형
- `SET('데이터값1', '데이터값2', ...)`: 여러 개를 저장할 수 있는 열거형

### 시간

- `DATE`: 'YYYY-MM-DD'
- `DATETIME`: 'YYYY-MM-DD HH:MM:SS'
- `TIMESTAMP`: 별도의 입력이 없으면 데이터가 입력된 시간이 저장됨
- `TIME`: 'HH:MM:SS'나 'HHH:MM:SS'
- `YEAR(4)`: 4자리 연도

## 집계 함수

- `COUNT`
- `MIN`, `MAX`
- `SUM`
- `AVG`

`GROUP BY`로 결과를 그룹화할 수 있다. `HAVING`과 같이 사용하여 결과의 조건을 지정할 수 있다.

```mysql
SELECT COUNT(*) AS NumberOfRervation 
FROM Reservation;

SELECT Address, Name, MAX(Age) AS MaxAge 
FROM Customer 
GROUP BY Address 
HAVING MaxAge > 15;
```

## 흐름 제어

### `IF()`

```mysql
IF(expr1, expr2, expr3)
```

`expr1`이 참이면 `expr2`를 반환하고, 거짓이면 `expr3`을 반환한다.

### `IFNULL()`

```mysql
IFNULL(expr1, expr2)
```

`expr1`의 값이 `NULL`이 아니면 `expr1`을 반환하고, `NULL`이면 `expr2`를 반환한다.

### `CASE`

프로그래밍 언어의 스위치문과 비슷하다.

```mysql
SELECT COUNT(*) AS "count", 
    CASE 
        WHEN number_grade > 90 THEN "A" 
        WHEN number_grade > 80 THEN "B" 
        WHEN number_grade > 70 THEN "C" 
        ELSE "F" 
    END as "letter_grade" 
FROM student_grades GROUP BY letter_grade;
```

## 다중 테이블 연산

### `JOIN`

```mysql
# INNER JOIN
SELECT column_list 
FROM table_1 
INNER JOIN table_2 
    ON join_condition;
    
# LEFT JOIN
SELECT column_list 
FROM table_1 
LEFT JOIN table_2 
    ON join_condition;
    
# RIGHT JOIN
SELECT column_list 
FROM table_1 
RIGHT JOIN table_2 
    ON join_condition;
    
# CROSS JOIN
SELECT select_list 
FROM table_1 
CROSS JOIN table_2;
```

### `UNION`

여러 개의 `SELECT` 문의 결과를 하나의 테이블이나 결과 집합으로 합친다.

```mysql
SELECT Name FROM Reservation 
UNION 
SELECT Name FROM Customer;
```

중복되는 레코드도 출력하고 싶다면 `UNION ALL`을 사용한다.

### 서브쿼리

서브쿼리(subquery)란 다른 쿼리 내부에 포함되어 있는 `SELETE` 문을 의미한다.

```mysql
SELECT ID, ReserveDate, RoomNum 
FROM Reservation 
WHERE Name IN (SELECT Name 
               FROM Customer
               WHERE Address = '서울');

SELECT Name, ReservedRoom 
FROM (SELECT Name, ReserveDate, (RoomNum + 1) AS ReservedRoom 
      FROM Reservation 
      WHERE RoomNum > 1001) AS ReservationInfo;
```

서브쿼리가 복잡한 `JOIN`보다 더 읽기 편할 때가 있다.

## 함수

### 수학

반올림은 `ROUND()`를 사용한다. 다음은 셋째 자리에서 반올림한다.

```mysql
SELECT ROUND(135.375, 2);
```

### 날짜

#### `DATE_ADD()`/`DATE_SUB()`

날짜나 시간을 더하거나 뺀다.

```mysql
SELECT DATE_ADD('2017-06-15', INTERVAL 10 DAY);
```

#### `DATE_FORMAT()`

날짜의 형식을 변경한다.

```mysql
SELECT DATE_FORMAT('2017-06-15', '%Y-%m-%d %h:%k:%s');
```

#### `DATEDIFF()`, `TIMEDIFF()`

두 날짜의 차이를 구한다. 앞 날짜에서 뒤 날짜를 뺀다.

```mysql
SELECT DATEDIFF("2017-06-25", "2017-06-15");
```

