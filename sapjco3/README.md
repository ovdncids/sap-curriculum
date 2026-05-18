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
Attributes > Remote-Enabled Module (RFC 활성화)
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
* 활성화(Ctrl + F3) 도중 Bypass 오류 나면 Active 버튼 누름, 실행(F8)

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
