# PI (Process Integration: 시스템 통합)
* [SAP PI (Process Integration) Overview](https://www.youtube.com/watch?v=ocIwEnSWdto&list=PLlnY1uYqqwZRj5x2ulqXUGJStzRbNm1BF)

## SAP Integration Suite (Cloud 환경, 최근 흐름)
* [30일 평가](https://www.sap.com/korea/products/technology-platform/integration-suite/trial.html)
```sh
로그인 > `GE...`로 시작하는 SAP Learning Academy ID와 Password 생성 (이메일: ge...@sapexperienceacademy.com)
Click here to start your basic trial! > {SAP Learning Academy ID} 로그인
Introduction to SAP Integration Suite
```

### Package 생성
* [Create the integration flow](https://trials.cfapps.eu10-004.hana.ondemand.com/learning-journey/bt-int-suite/iflow)
* [SAP Integration Suite](https://integration-suite-ap10.integrationsuite.cfapps.ap10.hana.ondemand.com/shell/home)
* 평가판이므로 모든 사용자에게 공유 된다.
```sh
SAP Integration Suite > Design > Integrations and APIs > Create
Name: orders_{SAP Learning Academy ID}
Technical Name: orders{SAP Learning Academy ID}
Short description: 설명
Save
```

### Copy Hello World
* [Hello World!](https://www.youtube.com/watch?v=xR1bSxtBsMo)
```sh
SAP Integration Suite > Design > Integrations and APIs > Search > Hello World
Artifacts > HelloWorld (상세로 이동하고 싶으면 '>'를 클릭) > Actions > Copy
Name: {SAP Learning Academy ID}_MSO_HelloWorld
Package: orders_{SAP Learning Academy ID}
Copy > Navigate > {SAP Learning Academy ID}_MSO_HelloWorld > 이름 클릭하고 상세로 이동
HTTPS 더블 클릭 > Connection > Edit > Address: {SAP Learning Academy ID}_MSO_HelloWorld (Endpoints가 이미 존재 할 수 있으므로 유니크하게 만듬)
Save > Deploy > Runtime Profile: Cloud Integration > Yes (상단에 `Runtime Status: Started` 성공)

# Endpoints 주소 얻기
도형 바탕 부분 더블 클릭 > Integration Flow > Deployment Status > Monitor Integration Content > Navigate
Or
SAP Integration Suite > Monitor > Integrations and APIs > Manage Integration Content > All > Search > {SAP Learning Academy ID}_MSO_HelloWorld
Endpoints 주소 확인
Undeploy로 삭제 가능
```
* MSO란 Message-Oriented Service Orchestration.

### Test
* [Authorization](https://trials.cfapps.eu10-004.hana.ondemand.com/learning-journey/bt-int-suite/api-1)
```sh
SAP Integration Suite > Test > APIs
Endpoints 주소
GET
Headers
  Name: Authorization
  Value: 위의 `Authorization`에서 `Header value`로 검색 `Basic `으로 시작됨
Send > Response Body > Hello World!
```
* 간혹 Authorization 해더값이 안들어가면 F12 개발자 도구에서 확인. (Basic 먼저 넣고 Send 후 나머지도 붙여 넣고 Send)

### SAP BTP Cockpit (Business Technology Platform, Cockpit: 조정석)
* [기본 SAP 계정으로 핸드폰 인증](https://account.hanatrial.ondemand.com/trial/#/home/trial)
* [SAP BTP Cockpit](https://account.hanatrial.ondemand.com/trial)
* [구독 서비스](https://www.youtube.com/watch?v=jOYBIwDMWKY)
* 하지만 기본 SAP 계정에 SAP BTP Cockpit 권한 신청한것이라서 SAP Learning Academy 계정에는 적용 되지 않는다.

### Mock Data
* https://mockapi.io
* https://refapp-espm-ui-cf.cfapps.eu10.hana.ondemand.com/espm-cloud-web/espm.svc/
* https://refapp-espm-ui-cf.cfapps.eu10.hana.ondemand.com/espm-cloud-web/espm.svc/Products
