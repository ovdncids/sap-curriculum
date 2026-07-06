# HANA DB
## 접속
```sh
# DBeaver
Edition: Generic
Host: vhcala4hci
Port: 30213
Username: SYSTEM
Password: Ldtf5432
```

## DDIC - 메타 데이터
* [DDIC(Data Dictionary) - Database table](https://github.com/ovdncids/sap-curriculum/tree/master/sapjco3#ddicdata-dictionary---database-table)
* HANA DB에 없고 DDIC 객체임
```abap
* Table/View/Structrue 등의 기본 정보
SELECT *
* TABNAME, TABCLASS, AS4USER
FROM DD02L
INTO TABLE @DATA(lt_dd021)
WHERE TABNAME IN ('ZOT_CPK_SWAP_TST', 'ZST_CPK_SWAP_TST', 'ZTT_CPK_SWAP_TST').

* 필드(컬럼 정보)
SELECT *
* FIELDNAME, ROLLNAME, POSITION
FROM DD03l
INTO TABLE @DATA(lt_dd031)
WHERE TABNAME = 'ZST_CPK_SWAP_TST'
ORDER BY POSITION.

* Tyable Type 정보
SELECT *
* TYPENAME, ROWTYPE, AS4USER
FROM DD40L
INTO TABLE @DATA(lt_dd40l)
WHERE TYPENAME = 'ZTT_CPK_SWAP_TST'.

* Data Element 정보
SELECT *
* ROLLNAME, DOMNAME, AS4USER
FROM DD04L
INTO TABLE @DATA(lt_dd04l)
WHERE ROLLNAME LIKE 'Z%'.
```
