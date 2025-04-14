# 8장: SQL 응용

## 102섹션: SQL-DDL

**DDL**(Data Define Language, 데이터 정의어)은 DB를 구축하거나 수정할 목적으로 사용하는 언어이다.

DDL의 종류는 다음과 같다.

- **CREATE**
- **DROP**
- **ALTER**

다음은 DDL을 사용하는 예시이다.

```sql
CREATE TABLE 학생
       (이름 VARCHAR(15) NOT NULL,
        학번 CHAR(8),
        전공 CHAR(5),
        PRIMARY KEY(학번),
        FOREIGN KEY(전공) REFERENCES 학과(학과코드)
          ON DELETE SET NULL
          ON UPDATE CASCADE,
        CONSTRAINT 생년월일제약
          CHECK(생년월일>='1980-01-01'));
```

```sql
CREATE UNIQUE INDEX 고객번호_idx
ON 고객(고객번호 DESC);
```

```sql
CREATE VIEW 강좌교수(강좌명, 강의실, 수강제한인원, 교수이름)
AS SELECT 강좌명, 강의실, 수강인원, 이름
FROM 강좌, 교수
WHERE 강좌.교수번호=교수.교수번호
WITH CHECK OPTION;
```

```sql
ALTER TABLE 학생 ADD 학년 VARCHAR(3);
ALTER TABLE 학생 ALTER 학번 VARCHAR(10) NOT NULL;
```

```sql
DROP TABLE 학생 CASCADE;
```

## 103섹션: SQL-DCL

**DCL**(Data Control Language, 데이터 제어어)은 데이터의 보안, 무결성, 회복, 병행 제어 등을 정의하는 데 사용하는 언어이다. 데이터베이스 관리자(DBA)가 데이터 관리를 목적으로 사용한다.

DCL의 종류는 다음과 같다.

- COMMIT
- ROLLBACK
- GRANT
- REVOKE

다음은 DCL을 사용하는 예시이다.

```sql
GRANT RESOURCE TO NABI;
GRANT CONNECT TO STAR;
GRANT ALL ON 고객 TO NABI WITH GRANT OPTION;
```

```sql
REVOKE SELECT, INSERT, DELETE ON 교수 FROM 임꺽정 CASCADE;
```

```sql
COMMIT;
ROLLBACK;
SAVEPOINT S1;
ROLLBACK TO S1;
```

## 104섹션: SQL-DML

**DML**(Data Manipulation Language, 데이터 조작어)은 데이터베이스 사용자가 저장된 데이터를 관리하는데 사용하는 언어이다.

DML의 종류는 다음과 같다.

- SELECT
- INSERT
- DELETE
- UPDATE

```sql
INSERT INTO 테이블명([속성명1, ...]) VALUES (데이터1, ...);
```

```sql
DELETE FROM 테이블명 [WHERE 조건];
```

```sql
UPDATE 테이블명 SET 속성명 = 데이터, ... [WHERE 조건];
```

## 105섹션: DML-SELECT-1

```sql
SELECT [PREDICATE] [테이블명.]속성명 [AS 별칭], ...
FROM 테이블명, ...
[WHERE 조건]
[ORDER BY 속성명 [ASC | DESC]];
```

```sql
SELECT *
FROM 사원
WHERE 이름 LIKE "김%";
```

```sql
SELECT *
FROM 사원
WHERE EXISTS (SELECT ...);
```

```sql
SELECT *
FROM 사원
WHERE 이름 NOT IN (SELECT 이름 FROM 여가활동);
```

```sql
SELECT *
FROM 사원
WHERE 기본급 < ALL (SELECT 기본급 FROM 사원 WHERE 주소 = "망원동");
```

## 106섹션: DML-SELECT-2

```sql
SELECT ...
[, 그룹함수(속성명) [AS 별칭]]
[, WINDOW함수 OVER (PARTITION BY 속성명1, ... ORDER BY 속성명3, ...) [AS 별칭]]
FROM 테이블명, ...
[WHERE 조건]
[GROUP BY 속성명, ...]
[HAVING 조건]
[ORDER BY 속성명 [ASC | DESC]];
```

다음은 GROUP BY와 함께 사용하는 **그룹 함수**의 종류이다.

- COUNT
- SUM
- AVG
- MAX
- MIN
- STDDEV
- VARIANCE
- ROLLUP: n+1 레벨. 하위 레벨에서 상위 레벨 순
- CUBE: $2^n$ 레벨. 상위 레벨에서 하위 레벨 순

ROLLUP과 CUBE 두 함수는 GROUP BY 뒤에 위치한다.

다음은 WINDOW 함수의 종류이다.

- ROW_NUMBER()
- RANK(): 공동 순위 있음
- DENSE_RANK(): 공동 순위 무시

**집합 연산자**를 사용하여 2개 이상의 테이블을 하나로 통합할 수 있다. 다음은 집합 연산자의 종류이다.

- UNION: 합집합
- UNION ALL: 합집합(중복 행도 출력)
- INTERSECT: 교집합
- EXCEPT: 차집합

```sql
SELECT A FROM R
UNION
SELECT A FROM S
ORDER BY A DESC;
```

## 107섹션: DML-JOIN

- INNER JOIN
  - **세타 조인**
  - **동등 조인**
  - **자연 조인**: 동등 조인에서 중복된 속성 제거
- OUTER JOIN
  - LEFT OUTER JOIN
  - RIGHT OUTER JOIN
  - FULL OUTER JOIN

다음은 INNER JOIN의 예시이다. 세 SQL문은 동일한 결과를 출력한다.

```sql
SELECT 학번, 이름, 학생.학과코드, 학과명
FROM 학생, 학과
WHERE 학생.학과코드 = 학과.학과코드;

SELECT 학번, 이름, 학생.학과코드, 학과명
FROM 학생 NATURAL JOIN 학과;

SELECT 학번, 이름, 학생.학과코드, 학과명
FROM 학생 JOIN 학과 USING(학과코드);
```

다음은 OUTER JOIN의 예시이다. 두 SQL문은 동일한 결과를 출력한다.

```sql
SELECT 학번, 이름, 학생.학과코드, 학과명
FROM 학생 LEFT OUTER JOIN 학과
ON 학생.학과코드 = 학과.학과코드;

SELECT 학번, 이름, 학생.학과코드, 학과명
FROM 학생, 학과
WHERE 학생.학과코드 = 학과.학과코드(+);
```

ON을 기억할 필요가 있다.
