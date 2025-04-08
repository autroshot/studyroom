# 3장: SQL 응용

## 79섹션: SQL의 개념

SQL(Structured Query Language)은 관계형 데이터베이스의 표준 질의어로 정의, 조작, 제어 기능이 있다.

### DDL(Data Define Language, 데이터 정의어)

- CREATE
- ALTER
- DROP

### DML(Data Manipulation Language, 데이터 조작어)

- SELECT
- INSERT
- DELETE
- UPDATE

### DCL(Data Control Language, 데이터 제어어)

- COMMIT
- ROLLBACK
- GRANT
- REVOKE

## 80섹션: DDL

DDL(Data Define Language, 데이터 정의어)은 DB 구조, 데이터 형식, 접근 방식 등 DB를 구축하거나 수정하는 언어이다.

- CREATE (SCHEMA | DOMAIN | TABLE | VIEW | INDEX)
- ALTER TABLE
  - ADD
  - ALTER
  - DROP COLUMN
- DROP (SCHEMA | DOMAIN | TABLE | VIEW | INDEX | CONSTRAINT) [CASCADE | RESTRICT]

## 81섹션: DCL

DCL(Data Control Language, 데이터 제어어)은 데이터의 보안, 무결성, 회복, 병행 제어 등을 정의하는 데 사용하는 언어이다. **데이터베이스 관리자(DBA)**가 데이터 관리를 목적으로 사용한다.

- GRANT/REVOKE 사용자등급 TO/FROM 사용자\_ID\_리스트
- GRANT 권한_리스트 **ON** 개체 TO 사용자 [WITH GRANT OPTION]
- REVOKE [GRANT OPTION FOR] 권한_리스트 **ON** 개체 FROM 사용자 [CASCADE]
- COMMIT: 트랜잭션이 성공적으로 끝나면 데이터베이스가 새로운 일관성(Consistency) 상태를 가지기 위해 COMMIT으로 모든 내용을 데이터베이스에 반영한다.
- ROLLBACK: 아직 커밋되지 않은 변경된 모든 내용들을 취소한다. 데이터베이스가 비일관성(Inconsistency) 상태가 되지 않도록 일부분만 완료된 트랜잭션은 롤백되어야 한다. 롤백 위치를 지정하지 않으면 최근 커밋 지점으로 돌아간다.
- SAVEPOINT: 트랜잭션 내에 롤백할 위치인 저장점을 지정한다.

## 82섹션: DML

DML(Data Manipulation Language, 데이터 조작어)은 데이터베이스 사용자가 응용 프로그램이나 질의어를 통해 저장된 데이터를 관리하는데 사용하는 언어이다.

- **INSERT INTO** 테이블명([속성명1, ...]) **VALUES** (데이터1, ...);
- **DELETE FROM** 테이블명 [WHERE 조건];
- **UPDATE** 테이블명 **SET** 속성명 = 데이터, ... [WHERE 조건];

## 83섹션: DML-SELECT-1

```sql
SELECT [PREDICATE] [테이블명.]속성명 [AS 별칭], ...
FROM 테이블명, ...
[WHERE 조건]
[ORDER BY 속성명 [ASC | DESC]];
```

```sql
SELECT *
FROM 사원
WHERE 이름 NOT IN (SELECT 이름 FROM 여가활동);
```

```sql
SELECT *
FROM 사원
WHERE EXISTS (SELECT ...);
```

## 84섹션: DML-SELECT-2

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

다음은 **GROUP BY와 함께 사용하는 그룹 함수**의 종류이다.

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

다음은 **WINDOW 함수**의 종류이다.

- ROW_NUMBER()
- RANK(): 공동 순위 있음
- DENSE_RANK(): 공동 순위 무시

**집합 연산자**를 사용하여 2개 이상의 테이블을 하나로 통합할 수 있다. 다음은 집합 연산자의 종류이다.

- UNION: 합집합
- UNION ALL: 합집합(중복 행도 출력)
- INTERSECT: 교집합
- EXCEPT: 차집합

