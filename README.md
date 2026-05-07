# SAP

## Docker SAP S/4HANA Trial@1909 설치
* [Docker SAP S/4HANA Trial@1909 설치](https://github.com/ovdncids/sap-curriculum/blob/master/Install.md)

## 사용자 추가
* https://www.youtube.com/watch?v=Otm8svqKfvk&t=453s
```sh
DEVELOPER 로그인 > 트랜젝션 Inputbox > SU01
User: 사용자명 입력 후 Create(F8) 아이콘
Address > Title, Last name, First name 입력
Logon Data > Alias, New Password, Repeat Password
Defaults > Date Format > YYYY-MM-DD
Profiles > Profile 입력창 > SAP_ALL > 엔터
Exit > Save user
```

## 더미 데이터 생성
* https://www.youtube.com/watch?v=Otm8svqKfvk&t=453s
```sh
# 생성
새로운 사용자 로그인 > 트랜젝션 Inputbox > SE38
Program: SAPBC_DATA_GENERATOR > 실행(F8)
Standard Data Record 선택 > 실행(F8) > Yes

# 조회
트랜젝션 Inputbox > SE11
Database table: SFLIGHT > 조회(Display: F7)
Contents (Data Browser: Ctrl + Shift + F10) 누르기
Data Browser > 실행(F8)
상단 메뉴 Settings > User Parameters...(F8) > ALV Grid Display 선택
```

## 용어
```sh
# 메인 모듈
CO: 원가관리
FI: 재무회계
SD: 영업/물류
PP: 생산 관리
MM: 자재/구매
BC: Basis (SAP 시스템의 설치, 운영, 관리, 기술 인프라를 담당하는 영역)

# 서브 모듈
QM: 품질
PM: 설비
LE: 문류
EWM: 창고관리
PS: 프로젝트
HR: 인사
TR: 자금
MDG: 데이터관리
```

## ABAP
```sh
사용자 로그인 > 트랜젝션 Inputbox > SE80 (ABAP Workbench)
Local Objects (자신의 계정에서만 사용 가능) > 사용자
$TMP_{사용자} > Create > Program > ZTEST001 (커스텀 프로그램은 Z나 Y로 시작)
Title: 한글명 가능, Type: Executable program (실행가능 프로그램) > Save
Package: $TMP > Local Object
$TMP_{사용자} > Program > ZTEST001 > Change (프로그램 수정)
```

* 프로그램 수정
```abap
REPORT ZTEST001.
WRITE: 'Hello'.
WRITE:/ 'World'.
*/(줄바꿈)
*활성화(Ctrl + F3), 실행(F8)

CL_DEMO_OUTPUT=>BEGIN_SECTION('H1').
CL_DEMO_OUTPUT=>BEGIN_SECTION('H2').
CL_DEMO_OUTPUT=>BEGIN_SECTION('H3').
CL_DEMO_OUTPUT=>DISPLAY('내용').
```

## ABAP Runtime Errors
```sh
사용자 로그인 > 트랜젝션 Inputbox > ST22 (/OST22: 현재창 말고 새로운 창에서 ST22 열기)
```
