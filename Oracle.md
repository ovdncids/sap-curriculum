# Oracle
* [설치](https://github.com/ovdncids/mysql-curriculum/blob/master/Oracle.md)

## Entry 등록
```sh
T-Code: DBCO

Connection Name : ZORACLE
DBMS            : ORA
Connection Info : //localhost:1521/FREEPDB1
Connection Info : "localhost:1521/FREEPDB1"
```
* `First`는 Hana DB이고, `Secondary DB` 형식으로 Entry 등록

## 접속 테스트
```sh
T-Code: DBACOCKPIT

DB Connections > Oracle > ZORACLE > Test
```
* 현재 Test 실패 `-- ERROR Database connection ZORACLE: ADBC error 'Internal error      8.192  has occured'`

## 프로그램
```abap
REPORT ztest_oracle.

DATA lv_count TYPE i.

EXEC SQL.
  CONNECT TO 'ZORACLE'
ENDEXEC.

EXEC SQL.
  SELECT COUNT(*)
    INTO :lv_count
    FROM DUAL
ENDEXEC.

WRITE: / lv_count.
```
