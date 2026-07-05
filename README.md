# SAP

## Docker SAP S/4HANA Trial@1909 설치
* [Docker SAP S/4HANA Trial@1909 설치](https://github.com/ovdncids/sap-curriculum/blob/master/Install.md)

## 단축키
```sh
Ctrl: 현재 포커스 위치를 알려줌
Ctrl + y: 여러줄의 텍스트를 선택 가능하게 변경 (마우스 포인터가 변경 됨)
Ctrl + ,: 주석
Ctrl + .: 주석 해제
Ctrl + 클릭: 명령어, 변수명 전체 선택
```

## 사용자 추가
* https://www.youtube.com/watch?v=Otm8svqKfvk&t=453s
```sh
DEVELOPER 로그인 > T-Code: SU01
User: 사용자명 입력 후 Create(F8) 아이콘
Address > Title, Last name, First name 입력
Logon Data > Alias, New Password, Repeat Password
Defaults > Date Format > YYYY-MM-DD
Profiles > Profile 입력창 > SAP_ALL > 엔터
Save 버튼 또는 Exit > Save user
```
* 로그인 할때 비밀번호를 변경하므로 1회성 비밀번호 사용

## 더미 데이터 생성
* https://www.youtube.com/watch?v=Otm8svqKfvk&t=453s
```sh
# 생성
새로운 사용자 로그인 > T-Code: SE38
Program: SAPBC_DATA_GENERATOR > 실행(F8)
Standard Data Record 선택 > 실행(F8) > Yes

# 조회
T-Code: SE11
Database table: SFLIGHT > 조회(Display: F7)
Contents (Data Browser: Ctrl + Shift + F10) 누르기
Data Browser > 실행(F8)
상단 메뉴 Settings > User Parameters...(F8) > ALV Grid Display 선택
```

## 용어
```sh
# 메인 모듈
CO (Controlling): 원가관리
FI: 재무회계
SD: 영업/물류
PP: 생산 관리
MM: 자재/구매
BC (Basis): (SAP 시스템의 설치, 운영, 관리, 기술 인프라를 담당하는 영역)

# 서브 모듈
QM: 품질
PM: 설비
LE: 문류
EWM: 창고관리
PS (Project System): 프로젝트 (집짓는 프로젝트)
WBS (Work Breakdown Structure): Breakdown: 잘게 나누다. 프로젝트 세부 작업 번호 (전기 공사, 배관 공사)
  # PS > WBS > Network > Activity
HR: 인사
TR: 자금
MD (Master Data): 기준 데이터
MDG: 데이터관리

# 관련 용어
PI (Process Integration): 회사 시스템들의 연결
```

## ABAP
### 프로그램 생성
```sh
사용자 로그인 > T-Code: SE80 (ABAP Workbench)
Local Objects (자신의 계정에서만 사용 가능) > 사용자
$TMP_{사용자} > Create > Program > ZTEST001 (커스텀 프로그램은 Z나 Y로 시작)
Title: 한글명 가능, Type: Executable program (실행가능 프로그램) > Save
Package: $TMP > Local Object
$TMP_{사용자} > Program > ZTEST001 > Change (프로그램 수정)
```

### 프로그램 수정
```abap
REPORT ZTEST001.

* 브레이크 포인트
BREAK-POINT.

* 결과창 (Debug 모드 종료 후 확인 가능)
SKIP to LINE 10. " Y축 Offset
POSITION 40.     " X축 Offset
WRITE: 'Hello'.
WRITE:/ 'World'. " /(줄바꿈)

* Alert창과 유사함 (Debug 모드 진행중 확인 가능)
MESSAGE 'Debug 확인' TYPE 'I'.

* OUTPUT창 (Debug 모드 종료 후 확인 가능)
CL_DEMO_OUTPUT=>BEGIN_SECTION('H1').
CL_DEMO_OUTPUT=>BEGIN_SECTION('H2').
CL_DEMO_OUTPUT=>BEGIN_SECTION('H3').
CL_DEMO_OUTPUT=>DISPLAY('내용').
```
* 활성화(Ctrl + F3), 실행(F8)
* `디버거 모드 > 우측 하단 > 변수명 > 더블클릭` 값을 확인 하고 `Back(F3)`

### 팝업창
```abap
REPORT ZTEST001.

* 팝업 스크린 정의 (1100 = 커스텀을 뜻함, 9999까지 사용 가능)
SELECTION-SCREEN BEGIN OF SCREEN 1100.
  PARAMETERS input1(12) TYPE c DEFAULT 'Hello'.
  * PARAMETERS input1 TYPE char12 DEFAULT 'Hello'.
  PARAMETERS input2 TYPE string DEFAULT 'World'.
SELECTION-SCREEN END OF SCREEN 1100.
* 팝업 스크린 부르기 (0 0 = X Y 팝업 좌표)
CALL SELECTION-SCREEN 1100 STARTING AT 0 0.

* 실행 후 팝업창의 Execute(F8) 버튼을 누르면 sy-subrc 값은 0이 됨
IF sy-subrc <> 0.
  LEAVE PROGRAM.
ENDIF.

MESSAGE input1 TYPE 'I'.
WRITE input2.
```

## ABAP Runtime Errors, Logs
```sh
T-Code: ST22 (/OST22: 현재창 말고 새로운 창에서 ST22 열기, /NST22: 현재창 종료 후 ST22 이동)

# 로그 분석
SLG1 / ST05 / SAT
```

## Package 생성
```sh
# Transport Organizer: 변경사항 배포 관리 시스템
T-Code: SE01 > Create(F6) > Workbench request
Short Description: {리퀘스트 테스트 001} > Save
Modifiable > {A4HK9000?1) 사용자 {리퀘스트 테스트 001}
             {A4HK9000?2) 사용자 Uclassified > 클릭 > 상단 Request/Task > Change Type > Development/Correction

# Package 생
T-Code: SE80 > Package > {ZABAP_PACKAGE_TEST001} > 엔터 > Create
Short Description: {아밥 패키지 테스트001}, Package Type: Main Package > Save
Request 오른쪽 Browser 아이콘: {리퀘스트 테스트 001} 선택 > Save
```

## HANA DB 접속
```sh
# DBeaver
Edition: Generic
Host: vhcala4hci
Port: 30213
Username: SYSTEM
Password: Ldtf5432
```
