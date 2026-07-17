# PI (Process Integration: 시스템 통합)
* [SAP PI (Process Integration) Overview](https://www.youtube.com/watch?v=ocIwEnSWdto&list=PLlnY1uYqqwZRj5x2ulqXUGJStzRbNm1BF)

# SAP Integration Suite (Cloud 환경, 최근 흐름)
* [30일 평가](https://www.sap.com/korea/products/technology-platform/integration-suite/trial.html)
```sh
로그인 > `GE...`로 시작하는 ID와 Password 생성 (이메일: ge...@sapexperienceacademy.com)
Click here to start your basic trial! > `GE...` ID로 로그인
Introduction to SAP Integration Suite
```

## Unit 1
* [Create the integration flow](https://trials.cfapps.eu10-004.hana.ondemand.com/learning-journey/bt-int-suite/iflow)
* [SAP Integration Suite](https://integration-suite-ap10.integrationsuite.cfapps.ap10.hana.ondemand.com/shell/home)
* 평가판이므로 모든 사용자에게 공유 된다.
```sh
# Package 생성
SAP Integration Suite > Design > Integrations and APIs > Create
Name: orders_GE3...
Technical Name: ordersGE...
Short description: 설명
Save
```

* [Hello World!](https://www.youtube.com/watch?v=xR1bSxtBsMo)
```sh
# Copy
SAP Integration Suite > Design > Integrations and APIs > Search > Hello World
Artifacts > HelloWorld (상세로 이동하고 싶으면 '>'를 클릭) > Actions > Copy
Name: orders_GE3...HelloWorld_copy
Package: orders_GE3...
Copy > Navigate > orders_GE3...HelloWorld_copy > Created 클릭하고 상세로 이동
HTTPS 더블 클릭 > Connection > Edit > Address: ordersGE3HelloWorldCopy (Endpoint가 이미 존재 할 수 있으므로 유니크하게 만듬)
Save > Deploy > Runtime Profile: Cloud Integration > Yes (상단에 `Runtime Status: Started` 성공)
```
