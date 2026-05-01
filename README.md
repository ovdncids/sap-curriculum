# SAP

## Docker S/4HANA 설치
```sh
docker pull amitlaldocker/abaptrial:1909
```
```sh
docker run --stop-timeout 3600 -it --name a4h -h vhcala4hci --sysctl kernel.shmmni=32768 --ulimit nofile=1048576:1048576 -p 3200:3200 -p 3300:3300 -p 8443:8443 -p 30213:30213 -p 50000:50000 -p 50001:50001 amitlaldocker/abaptrial:1909 -skip-limits-check -agree-to-sap-license
```
