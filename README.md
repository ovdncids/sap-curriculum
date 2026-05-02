# SAP

## Docker S/4HANA 설치
* https://www.youtube.com/watch?v=j80h09W396M

### S/4HANA 이미지 다운로드
```sh
docker pull amitlaldocker/abaptrial:1909
```

* 윈도우
```sh
docker run --stop-timeout 3600 -it --name a4h -h vhcala4hci --sysctl kernel.shmmni=32768 --ulimit nofile=1048576:1048576 -p 3200:3200 -p 3300:3300 -p 8443:8443 -p 30213:30213 -p 50000:50000 -p 50001:50001 amitlaldocker/abaptrial:1909 -skip-limits-check -agree-to-sap-license
```

* Mac Silicon은 `--platform linux/amd64` 넣어야 함
* Docker Desktop > Settings > Resources > 메모리 16G 이상, 나머지도 전부 Max
```sh
docker run --platform linux/amd64 --stop-timeout 3600 -it --name a4h -h vhcala4hci --sysctl kernel.shmmni=32768 --ulimit nofile=1048576:1048576 -p 3200:3200 -p 3300:3300 -p 8443:8443 -p 30213:30213 -p 50000:50000 -p 50001:50001 amitlaldocker/abaptrial:1909 -skip-limits-check -agree-to-sap-license
```
* `!!! HDB license has expired !!!` 나오면 하드웨어적 성공
```sh
docker stop a4h --timeout 7200
```
* 안되면 Docker Desktop에서 정지

### 라이센스
* [라이센스 받기](https://go.support.sap.com/minisap/#/minisap)
* `Hardware Key`는 `!!! HDB license has expired !!!` 2줄 위에 있음
* A4H - SAP NetWeaver AS ABAP 7.4 and above (Linux / SAP HANA) > Generate 하면 `A4H_Multiple.txt` 파일 다운로드 됨
* HDB - SAP HANA Platform Edition (64GB) > Generate 하면 `HDB.txt` 파일 다운로드 됨
```sh
docker cp /{다운 경로}/HDB.txt a4h:/opt/sap/HDB_license
```
```sh
docker start a4h
docker logs -f a4h
```

```sh
docker cp /{다운 경로}/A4H_Multiple.txt a4h:/opt/sap/ASABAP_license
docker exec -it a4h /usr/local/bin/asabap_license_update
docker logs a4h
```
* `Have fun!` 나오면 성공 (다시 시작해도 `Have fun!`까지 기다려야 정상 작동 10분정도)

### S/4HANA 서버 터미널
```sh
docker exec -it a4h bash

# 정상적으로 서버가 `GREEN, Running` 인지 확인
/usr/sap/hostctrl/exe/sapcontrol -nr 00 -function GetProcessList
```

```sh
sudo vi /etc/hosts

# SAP S/4HANA
127.0.0.1 vhcala4hci
```
* https://vhcala4hci:50001/sap/bc/ui2/flp
* SAP* / Ldtf5432 / Down1oad
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
Client: 000
User: SAP*
Password: Ldtf5432
Logon Language: EN

Client: 001
User: DEVELOPER
Password: Ldtf5432
Logon Language: EN
```
