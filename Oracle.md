# Oracle
* [설치](https://github.com/ovdncids/mysql-curriculum/blob/master/Oracle.md)

## Entry 등록
* `First DB`는 Hana DB이고, `Secondary DB` 형식으로 Entry 등록
```sh
T-Code: DBCO

Connection Name : ZORACLE
DBMS            : ORA
Connection Info : "172.17.0.3:1521/FREEPDB1"
```
* Docker Desktop > oracle > Inspect > Networks > bridge > IPAddress (Docker SAP와 같은 Gateway이면 Ping 가능)

## 접속 테스트
```sh
T-Code: DBACOCKPIT

DB Connections > Oracle > ZORACLE > Test
```

### -- ERROR Database connection ZORACLE: ADBC error 'Internal error      8.192  has occured'
```sh
T-Code: SM21 > 에러 로그 확인

# Docker SAP
find /usr/sap -name "dboraslib*"
ldd /usr/sap/A4H/D00/exe/dboraslib.so
libclntsh.so.11.1 => not found
```
* [Oracle Instant Client Downloads for Linux x86-64 (64-bit) - Version 11.2.0.4.0](https://www.oracle.com/kr/database/technologies/instant-client/linux-x86-64-downloads.html)
```sh
# Local
docker cp /{다운 경로}/oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm a4h:/opt/sap/oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm

# Docker SAP
rpm -ivh /opt/sap/oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm
find / -name "libclntsh.so*"
ln -s /usr/lib/oracle/11.2/client64/lib/libclntsh.so.11.1 /lib64/libclntsh.so.11.1

ldd /usr/sap/A4H/D00/exe/dboraslib.so
libnnz11.so => not found
ln -s /usr/lib/oracle/11.2/client64/lib/libnnz11.so /lib64/libnnz11.so
```

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
