# SAP Java Connector@3.0.14

## SAP
### RFC (Remote Function Call)에 사용할 Function 만들기
```sh
T-Code: SE80
# Function Group 만들기
Function Group > ZFG_TEST > 생성
Function group: ZFG_TEST
Short text: 테스트 함수 폴더 > 저장
Package: $TMP > 저장

# Function Module 만들기
ZFG_TEST > Create > Function Module > Z_FUNCTION_TEST
Short text: 함수 테스트 > 저장

# 함수 속성 변경
Z_FUNCTION_TEST > Change
Import > Parameter Name: IV_INPUT, Typing: TYPE, Associated Type: STRING
Export > Parameter Name: EV_OUTPUT, Typing: TYPE, Associated Type: STRING
Source code
```

Z_FUNCTION_TEST
```abap
FUNCTION Z_FUNCTION_TEST.
*"----------------------------------------------------------------------
*"  주석을 Import, Export 바탕으로 자동 생성 됨 (Interface라 함)
*"----------------------------------------------------------------------
EV_OUTPUT = IV_INPUT && ' World!'.

ENDFUNCTION.
```

활성화
```sh
Inactive Objects for DEVLOPER 창 >
  `Local objects 탭` > Select All(F9) > Enter
# `Transportable Objects 탭`을 선택하면 이관 대상이라는 뜻
```
* 활성화(Ctrl + F3) 후 실행(F8) > IV_INPUT 값 입력 > 실행(F8) > 정상 동작 확인

RFC 활성화
```sh
# 함수 속성 변경
Z_FUNCTION_TEST > Change
Attributes > Remote-Enabled Module (RFC 활성화)
```
* 활성화 도중 Bypass 오류 나면 `Import > IV_INPUT > Pass by Value > 체크`, `Export` 동일

## pom.xml
```xml
<dependency>
    <groupId>me.saro</groupId>
    <artifactId>sap-jco-manager</artifactId>
    <version>3.0.14.7</version>
</dependency>
<dependency>
    <groupId>com.sap.conn.jco</groupId>
    <artifactId>sapjco3</artifactId>
    <version>3.0.14</version>
    <scope>system</scope>
    <systemPath>${basedir}/src/main/webapp/WEB-INF/lib/sapjco3.jar</systemPath>
    <!-- 현 github 하위 lib 폴더의 모든 파일을 ${basedir}/src/main/webapp/WEB-INF/lib에 복사  -->
</dependency>
```
* `/src/main/webapp/WEB-INF/lib`에 모든 파일을 복사해 넣는다.

## build.gradle.kts
```kts
dependencies {
    implementation(files("libs/sap-jco-manager.jar"))
    implementation(files("libs/sapjco3.jar"))
}
```
* `/libs`에 모든 파일을 복사해 넣는다.

## Java
```java
public class SapConnection {
    public static SapManager sapManager;

    public static SapManager getSapManager() throws IOException, JCoException {
        if (sapManager != null) return sapManager;
        Properties sapProperties = new Properties();
        sapProperties.setProperty(DestinationDataProvider.JCO_ASHOST, "vhcala4hci");
        sapProperties.setProperty(DestinationDataProvider.JCO_USER, "DEVELOPER");
        sapProperties.setProperty(DestinationDataProvider.JCO_PASSWD, "Ldtf5432");
        sapProperties.setProperty(DestinationDataProvider.JCO_SYSNR, "00"); // System Number 00: 첫번째 인스턴스, 01: 두번째 인스턴스, 02: 추가 인스턴스
        sapProperties.setProperty(DestinationDataProvider.JCO_CLIENT, "001");
        sapProperties.setProperty(DestinationDataProvider.JCO_LANG, "EN");

        // Tomcat 경로에는 work 폴더가 있다. SpringBoot가 사용하는 Tomcat 경로에는 conf 폴더가 없을 수 있다.
        String jcoDestinationsDir = System.getProperty("catalina.base") + File.separator + "work";
        System.out.println(jcoDestinationsDir);
        FileOutputStream fos;
        fos = new FileOutputStream(jcoDestinationsDir + File.separator + "SAP.jcoDestination", false);
        System.out.println(new File(jcoDestinationsDir + File.separator + "SAP.jcoDestination").getAbsolutePath());
        sapProperties.store(fos, "Connect to SAP destination.");
        fos.close();

        // jco.destinations.dir 경로의 SAP.jcoDestination 파일을 읽어서 SapManager 연결 객체를 생성한다.
        System.setProperty("jco.destinations.dir", jcoDestinationsDir);
        sapManager = SapManager.builder().build("SAP");
        return sapManager;
    }

    public static void connect() throws IOException, JCoException {
        SapManager sapManager = getSapManager();
        SapFunction function = sapManager.getFunction("Z_FUNCTION_TEST");
        function.getImportParameterList().setValue("IV_INPUT", "Hello");
        SapFunctionResult sapResult = function.execute();
        System.out.println(sapResult.getExportParameterList().getString("EV_OUTPUT"));
    }
}
```
* 실행 할 수 있는 `main()` 메서드에서 `SapConnection.connect();`
* 콘솔에 `Hello World!` 찍힘
* `M1`인 경우 `(have 'x86_64', need 'arm64e' or 'arm64e.v1' or 'arm64' or 'arm64')` 오류 발생시 `Intel JDK(x64)`으로 변경

## Abap 기본 문법
```abap
FUNCTION Z_BASIC_TEST.

* 1회성 Type
TYPES: BEGIN OF ty_cpk_swap_tst,
         a    TYPE i,
         b    TYPE i,
         name TYPE string,
       END OF ty_cpk_swap_tst.
* LOOP 문을 돌릴때 꼭 `ls_cpk_swap_tst` 이름으로 구조체를 받아야 사용해야 한다.
DATA: lt_cpk_swap_tst TYPE TABLE OF ty_cpk_swap_tst,
      ls_cpk_swap_tst TYPE ty_cpk_swap_tst.
* 하나의 Data만 넣을때
APPEND VALUE ty_cpk_swap_tst(
  a    = 1
  b    = 2
  name = '홍길동'
) TO lt_cpk_swap_tst.
* 여러개의 Data를 넣을때, #은 any를 뜻함
lt_cpk_swap_tst = VALUE #(
  BASE lt_cpk_swap_tst
  ( a = 3 b = 4 name = '이순신' )
).
* FUNCTION은 WRITE 문이 출력되지 않고, MESSAGE는 개발 목적으로만 사용한다.
LOOP AT lt_cpk_swap_tst INTO ls_cpk_swap_tst.
  MESSAGE |A: { ls_cpk_swap_tst-a }, B: { ls_cpk_swap_tst-b }, NAME: { ls_cpk_swap_tst-name }| TYPE 'I'.
ENDLOOP.

ENDFUNCTION.
```

## Open SQL - Secondary DB
* [Connect to Oracle](https://github.com/ovdncids/sap-curriculum/blob/master/ConnectToOracle.md)
### DDIC(Data Dictionary) - Database table
```sh
T-Code: SE11 (Dictionary)
Database table: ZOT_CPK_SWAP_TST > Create
  # 16자까지만 사용 가능하다.
Short Description: ZORACLE의 ZOT_CPK_SWAP_TST 테이블
Delivery and Maintenance
  Delivery Class: A (Application table (master and transaction data)
  Data Browser/Table View Editing: Display/Maintenance Allowed
Fields
  Field  Key    Data element
  A      Check  INT4
  B      Check  INT4
  NAME          CHAR200
Save > Package > $TMP > Save
상단 Technical Settings
  General Properties
    Data class: APPL0 (Master Data, Transparent Tables)
    Size category: 0 (가장 작은 크기)
    Buffering: Buffering Not Allowed
Save 후 뒤로가기
Activate (Warnings occurred during activation 떠도 Yes)
```
* 오라클에 [TB_CPK_SWAP_TST](https://github.com/ovdncids/mysql-curriculum/blob/master/Oracle.md#%EB%B3%B5%ED%95%A9-%EA%B8%B0%EB%B3%B8-%ED%82%A4composite-primary-key-%EC%8A%A4%EC%99%91swap) 테이블과 동일하게 `ZOT_CPK_SWAP_TST` 테이블 생성

```abap
FUNCTION Z_ORACLE_TEST.

TYPES: BEGIN OF ty_cpk_swap_tst,
         a    TYPE i,
         b    TYPE i,
         name TYPE string,
       END OF ty_cpk_swap_tst.
DATA: lt_cpk_swap_tst TYPE TABLE OF ty_cpk_swap_tst,
      ls_cpk_swap_tst TYPE ty_cpk_swap_tst.

SELECT
  A,
  B,
  NAME
FROM ZOT_CPK_SWAP_TST
CONNECTION ZORACLE
INTO CORRESPONDING FIELDS OF TABLE @lt_cpk_swap_tst.

ENDFUNCTION.
```
* 디버그 모드에서 `lt_cpk_swap_tst` 값 확인

### DDIC(Data Dictionary) - Data type - Table Type, Structure
```sh
T-Code: SE11 (Dictionary)
Data type: ZST_CPK_SWAP_TST > Create > Structure
Short Description: ZORACLE의 ZOT_CPK_SWAP_TST 테이블 Structure
Components
  Component  Typing Method  Component Type
  A          Types  INT4
  B          Types  INT4
  NAME       Types  CHAR200
Save > Package > $TMP > Save

Data type: ZTT_CPK_SWAP_TST > Create > Table type
Short Description: ZORACLE의 ZOT_CPK_SWAP_TST 테이블 Table Type
Line Type: ZST_CPK_SWAP_TST
Save > Package > $TMP > Save > 뒤로가기
Activate (Local objects 뜨면 전부 선택 후 Continue, Warnings occurred during activation 떠도 Yes)
```

### 1회성 타입을 DDIC - Data type으로 변경
```diff
- TYPES: BEGIN OF ty_cpk_swap_tst,
-          a    TYPE i,
-          b    TYPE i,
-          name TYPE string,
-        END OF ty_cpk_swap_tst.
- DATA: lt_cpk_swap_tst TYPE TABLE OF ty_cpk_swap_tst,
-       ls_cpk_swap_tst TYPE ty_cpk_swap_tst.
```
```abap
DATA: lt_cpk_swap_tst TYPE ztt_cpk_swap_tst,
      ls_cpk_swap_tst TYPE zst_cpk_swap_tst.
```

### Export 형식을 ZTT_CPK_SWAP_TST으로 추가 (Java에서 List<HashMap<String, Object>>로 받기 위해)
```sh
Z_ORACLE_TEST > Export
  Parameter Name: ET_DATA, Typing: TYPE, Associated Type: ZTT_CPK_SWAP_TST 입력 후 엔터
```
```abap
ET_DATA = lt_cpk_swap_tst.
```

### Java
```java
SapManager sapManager = getSapManager();
SapFunction function = sapManager.getFunction("Z_ORACLE_TEST");
SapFunctionResult sapResult = function.execute();
JCoTable exportTable = sapResult.getExportParameterList().getTable("ET_DATA");
List<HashMap<String, Object>> exportList = new ArrayList<>();
for (int i = 0; i < exportTable.getNumRows(); i++) {
    exportTable.setRow(i);
    HashMap<String, Object> row = new HashMap<>();
    row.put("A", exportTable.getInt("A"));
    row.put("B", exportTable.getInt("B"));
    row.put("NAME", exportTable.getString("NAME"));
    exportList.add(row);
}
```
