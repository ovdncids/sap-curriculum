# SAP

## Docker S/4HANA Trial@1909 설치
* https://www.youtube.com/watch?v=j80h09W396M
* https://www.youtube.com/watch?v=uwGnfSJxw0U

### S/4HANA 이미지 다운로드
```sh
docker pull amitlaldocker/abaptrial:1909
```

### 윈도우
* `C:\Users\<UserName>\.wslconfig`
```sh
[wsl2]
processors=0
memory=16GB
swap=16GB
```

* CMD에서 WSL2의 CPU와 Memory가 늘어났는지 확인
```cmd
wsl --shutdown
wsl
free -h
nproc
```

* S/4HANA Trial Container 생성
```sh
docker run --stop-timeout 3600 -it --name a4h -h vhcala4hci --sysctl kernel.shmmni=32768 --ulimit nofile=1048576:1048576 -p 3200:3200 -p 3300:3300 -p 8443:8443 -p 30213:30213 -p 50000:50000 -p 50001:50001 amitlaldocker/abaptrial:1909 -skip-limits-check -agree-to-sap-license
# 5분에서 10분정도 기다림
```

### Mac
* Docker Desktop > Settings > Resources > 메모리 16G 이상, 나머지도 전부 Max
* `Mac Silicon`의 경우 `--platform linux/amd64` 넣어야 함
```sh
docker run --platform linux/amd64 --stop-timeout 3600 -it --name a4h -h vhcala4hci --sysctl kernel.shmmni=32768 --ulimit nofile=1048576:1048576 -p 3200:3200 -p 3300:3300 -p 8443:8443 -p 30213:30213 -p 50000:50000 -p 50001:50001 amitlaldocker/abaptrial:1909 -skip-limits-check -agree-to-sap-license
# 5분에서 10분정도 기다림
```
* `!!! HDB license has expired !!!` 나오면 하드웨어적 성공

### Container 종료 후 라이센스 파일 적용 해야함
```sh
# Ctrl + C 2번 또는
docker stop a4h --timeout 7200
```
* 안되면 Docker Desktop에서 정지

### HDB 라이센스 받기
* https://go.support.sap.com/minisap/#/minisap
* Hardware Key: B1002322283 (`!!! HDB license has expired !!!` 2줄 위에 있음)
* HDB - SAP HANA Platform Edition (64GB) > Generate 하면 `HDB.txt` 파일 다운로드 됨

```sh
docker cp /{다운 경로}/HDB.txt a4h:/opt/sap/HDB_license
```

```sh
# Container 시작
docker start a4h
docker logs -f a4h
```
* `Have fun!` 나오면 HDB 라이센스 성공 `SAP*` 로그인 가능 (다시 시작 한다면 `Have fun!`까지 기다려야 정상 작동)
* `Hint: Container must have at least 16GB RAM available` 나온다면 Container 다시 실행 또는 재부팅

### A4H 라이센스 받기
* https://go.support.sap.com/minisap/#/minisap

```sh
# `Have fun!` 위에 로그를 보면
System ID. . . . : A4H
Hardware Key . . : {그때 그때 바뀜}        (of this computer)
```
* A4H - SAP NetWeaver AS ABAP 7.4 and above (Linux / SAP HANA) > Generate 하면 `A4H_Multiple.txt` 파일 다운로드 됨

```sh
# Container가 돌고 있는 상태에서
docker cp /{다운 경로}/A4H_Multiple.txt a4h:/opt/sap/ASABAP_license
docker exec -it a4h /usr/local/bin/asabap_license_update
```
* `2 SAP license key(s) successfully installed.` 이렇게 뜨면 재시작 없이 `DEVELOPER` 로그인 가능

### S/4HANA Trial 서버 bash 들어가기
```sh
docker exec -it a4h bash

# 정상적으로 서버가 `GREEN, Running` 인지 확인
/usr/sap/hostctrl/exe/sapcontrol -nr 00 -function GetProcessList
```

### hosts 설정
```sh
sudo vi /etc/hosts

# SAP S/4HANA
127.0.0.1 vhcala4hci
```
* https://vhcala4hci:50001/sap/bc/ui2/flp
* SAP* / Ldtf5432
* 간단한 로그인 확인 가능

### SAP GUI for Java@8.10 설치
* [SAP_GUI_for_Java.rar 다운로드](https://www.sap.com/products/try-sap/trials-downloads.html)
* Windows, Mac, Linux 버전별이 압축되어 있음
```sh
# 실행
내역: 사용할 이름
고급 > 전문가 모드 > 저장 버튼 활성화 됨
conn=/H/vhcala4hci/S/3200

# 저장 후 더블 클릭
Client: 001
User: SAP*
Password: Ldtf5432
Logon Language: EN

# DEVELOPER 로그인
Client: 001
User: DEVELOPER
Password: Ldtf5432
Logon Language: EN
```

### DEVELOPER 로그인 시 Logon not possible (error in license check) 뜰 때
* SAP* 로그인 > 트랜젝션 Inputbox > SLICENSE > Active Hardware Key 복사 (B1002322283와 다른지 확인)
* 새로운 `Active Hardware Key`로 `A4H_Multiple.txt` 다시 다운로드

```sh
# Container가 돌고 있는 상태에서
docker cp /{다운 경로}/A4H_Multiple.txt a4h:/opt/sap/ASABAP_license
docker exec -it a4h /usr/local/bin/asabap_license_update
```
* `2 SAP license key(s) successfully installed.` 이렇게 뜨면 재시작 없이 `DEVELOPER` 로그인 가능

### 사용자 관리
```sh
SAP* 로그인 > 트랜젝션 Inputbox > SU01
```
