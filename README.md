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
docker stop -t 7200 a4h
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
* `Have fun!` 나오면 성공

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
* DEVELOPER / Down1oad
