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
* 활성화 도중 Bypass 오류 나면 Active 버튼 누름 > 정상 동작 확인

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

## Open SQL
### Secondary DB
* [Connect to Oracle](https://github.com/ovdncids/sap-curriculum/blob/master/ConnectToOracle.md)
#### DDIC(Data Dictionary)
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
*"----------------------------------------------------------------------
*"*"Local Interface:
*"----------------------------------------------------------------------

TYPES: BEGIN OF ty_test_pk,
         a    TYPE i,
         b    TYPE i,
         name TYPE string,
       END OF ty_test_pk.

DATA: lt_data_test_pk TYPE TABLE OF ty_test_pk,
      ls_data_test_pk TYPE ty_test_pk.

SELECT
  A,
  B,
  NAME
FROM ZOT_TEST_PK
CONNECTION ZORACLE
INTO CORRESPONDING FIELDS OF TABLE @lt_data_test_pk.



*REFRESH et_output.
*LOOP AT lt_data_test_pk INTO ls_data_test_pk.
*  APPEND |{ ls_data_test_pk-a }-{ ls_data_test_pk-b }-{ ls_data_test_pk-name }| TO EV_OUTPUT.
*ENDLOOP.



DATA: lv_buffer TYPE c.
LOOP AT lt_data_test_pk INTO ls_data_test_pk.
*  WRITE: ls_data_test_pk-a.
  MESSAGE ls_data_test_pk-name TYPE 'I'.
ENDLOOP.

ENDFUNCTION.
```
