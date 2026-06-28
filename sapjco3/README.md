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

<!--
FUNCTION Z_FUNCTION_ORACLE_TEST.
*"----------------------------------------------------------------------
*"*"Local Interface:
*"  EXPORTING
*"     REFERENCE(EV_OUTPUT) TYPE  STRING
*"----------------------------------------------------------------------

TYPES: BEGIN OF ty_test_pk,
         a    TYPE i,
         b    TYPE i,
         name TYPE string,
       END OF ty_test_pk.

DATA: lt_data_test_pk TYPE TABLE OF ty_test_pk,
      ls_data_test_pk TYPE ty_test_pk.

EXEC SQL.
  CONNECT TO 'ZORACLE'
ENDEXEC.

SELECT
  A,
  B,
  NAME
FROM ZOT_TEST_PK
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

------------------
DBCO 세팅까지 완료하셨다니 큰 산은 넘으셨네요!ZOT_TEST_PK라는 이름으로 Open SQL을 사용하시려면, SAP의 DDIC(Data Dictionary)에 테이블을 생성해 주어야 합니다. SAP가 이 이름을 인식해야 ABAP 컴파일러가 에러를 내지 않거든요.오라클에 있는 테이블 구조와 똑같이 SAP에 투명 테이블(Transparent Table)로 만들어주는 과정입니다. 세팅 절차를 알기 쉽게 정리해 드렸습니다.1단계: SAP GUI에서 테이블 생성 (SE11)트랜잭션 코드 SE11을 실행합니다.Database table 입력창에 ZOT_TEST_PK를 입력하고 Create 버튼을 누릅니다.Delivery and Maintenance 탭 설정:Short Description: 테이블 설명 적기 (예: Oracle Test Table)Delivery Class: A (Application table)Data Browser/Table View Maint.: Display/Maintenance Allowed (나중에 데이터 확인하기 편합니다)2단계: 필드(Fields) 정의하기 (중요)Fields 탭으로 이동해서 오라클의 TEST_PK 테이블 구조와 똑같이 매핑해 줍니다. SAP 내장 데이터 타입을 사용해 아래 테이블처럼 입력하세요.FieldKeyInitial ValuesData Element (또는 기본 타입 직접 지정)DescriptionMANDTMANDTClient (SAP 기본 필수 필드)AINT4 (또는 Built-in Type INT4)오라클의 A (INT)BINT4 (또는 Built-in Type INT4)오라클의 B (INT)NAMECHAR200 (또는 Built-in Type CHAR, Length 200)오라클의 NAME💡 꿀팁: Data Element 자리에 기존 엘리먼트가 없다면, 상단 메뉴의 Built-in Type 버튼을 누르고 INT4나 CHAR 타입을 직접 입력하면 편합니다.또한 오라클 Primary Key가 (A, B)였으므로 SAP에서도 A와 B 행의 Key 체크박스에 체크를 해줍니다.3단계: 기술적 속성(Technical Settings) 설정상단 메뉴바에서 Technical Settings 버튼(또는 Ctrl + Shift + F9)을 누릅니다.아래 항목을 입력하고 저장(Ctrl + S) 후 뒤로 가기(F3) 합니다.Data class: APPL0 (Master data, transparent tables)Size category: 0 (가장 작은 크기 설정)Buffering: Buffering Not Allowed4단계: 활성화(Activation)모든 설정을 마쳤다면 상단 메뉴의 체크 버튼(Ctrl + F2)을 눌러 문법 에러가 없는지 확인합니다.활성화 버튼(Ctrl + F3)을 눌러 테이블을 완전히 활성화합니다.

-->
