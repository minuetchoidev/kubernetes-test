# master 가상 환경 구성

## 사용도구

- VMware16
- CentOS Stream 9
- MobaXterm

![1](../assets/vmnet8(nat).png)

[Centos Stream 9 'x86_64' 다운로드 (구글 검색: centos download)](https://www.centos.org/download)

[MobaXterm Home Edition 'Portable editinon' 다운로드 (구글 검색: MobraXterm homeedition download)](https://mobaxterm.mobatek.net/download-home-edition.html)


1. 다음 조건을 확인하여 가상 머신을 생성한다.

| 항목 | 내용 |
| :---: | :---: |
| 설치 경로 | 개인 폴더 → VM 이미지 → master |
| Memory | 2GB |
| Processors | 4 Core |
| Hard Disk | 30GB |
| CD/DVD | CentOS-Stream-10-lastest-x86_64_dvd1.iso |
| Network Adapter | NAT(Vmnet8) |