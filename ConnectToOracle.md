# Connect to Oracle
* [Oracle 설치](https://github.com/ovdncids/mysql-curriculum/blob/master/Oracle.md)

## Entry 등록
* `First DB`는 Hana DB이고, `Secondary DB` 형식으로 Entry 등록
```sh
T-Code: DBCO

Connection Name : ZORACLE
DBMS            : ORA
Connection Info : "172.17.0.3:1521/xe"
Connection Info : "172.17.0.3:1521/xe":::HR (스키마를 HR로 지정, DBACOCKPIT에서 수정 가능)
```
* Docker Desktop > oracle > Inspect > Networks > bridge > IPAddress (Docker SAP와 같은 Gateway이면 Ping 가능)

## 접속 테스트
```sh
T-Code: DBACOCKPIT

DB Connections > Oracle > ZORACLE > Test
  # 정보 수정 후에 Test 하면 다음번 `ZORACLE` 사용부터 바로 적용 된다.
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
apt update
apt install -y rpm
rpm -ivh /opt/sap/oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm
find / -name "libclntsh.so*"

# 1번 방법 (강제 복사)
cp /usr/lib/oracle/11.2/client64/lib/* /lib64
ldd /usr/sap/A4H/D00/exe/dboraslib.so

# 2번 방법 (라이브러리 등록)
echo "/usr/lib/oracle/11.2/client64/lib" > /etc/ld.so.conf.d/oracle11g.conf
ldconfig -p | grep libclntsh.so
  # 라이브러리에 libclntsh.so.11.1 파일이 등록되어 있는지 확인
ldconfig
  # /etc/ld.so.conf.d/oracle11g.conf 파일을 라이브러리에 등록
ldconfig -p | grep libclntsh.so
ldd /usr/sap/A4H/D00/exe/dboraslib.so
```
* `Oracle 11g`, `Oracle 23` 둘다 접속 가능

## 접속 확인 프로그램 (Native SQL 형식)
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
* 실행해서 결과가 `1`이 나오면 성공
