# 목차

1. [제1장 Kubernetes 실습 환경 구성](./docs/kubernetes실습환경구성.md)
1. [제2장 minikube 설치](./docs/minikube설치.md)
1. [제3장 Kubernetes 설치]()
1. [제4장 Kubernetes 컨테이너 실행하기]()
1. [제5장 Kubernetes 아키텍처]()
1. [제6장 Kubernetes 파드]()
1. [제7장 Kubernetes 컨트롤러]()
1. [제8장 Kubernetes 서비스]()
1. [제9장 Kubernetes 인그레스]()
1. [제10장 Kubernetes 레이블과 애너테이션]()
1. [제11장 Kubernetes 컨피그맵]()
1. [제12장 Kubernetes 시크릿]()
1. [제13장 Kubernetes 스토리지]()
1. [제14장 Kubernetes VScode 연동]()

<br><br>

# 용어

- NAT (Network Address Translation):
    NAT는 "네트워크 주소 변환" 이라는 뜻입니다. 쉽게 비유하자면 '아파트의 대표 주소와 호수' 시스템과 같습니다.
    - 외부(인터넷): 아파트의 전체 주소만 압니다. 각 방(VM)에 누가 사는지 직접적으로는 모릅니다.
    - 내부(VM): 각 VM은 사설IP(예:192.168.x.x)를 가집니다.
    - 역할: VM이 외부 인터넷에 접속하고 싶을 때, VMware가 '대표IP'로 주소를 바꿔서 대신 데이터를 보내줍니다. 덕분에 VM들은 내 PC의 IP하나를 공유해서 인터넷을 사용할 수 있습니다.

- VMnet8이란?
    VMnet8은 VMware가 설치될 때 자동으로 생성되는 **가상 스위치**의 이름입니다.
    - 왜 8번인가요?: 특별한 기술적 이유는 없고, VMware가 NAT 전용 네트워크에 관습적으로 할당한 번호입니다. (참고로 VMnet0은 브릿지, VMnet1은 호스트 전용으로 쓰입니다.)
    - 특징: VMnet8에 연결된 모든 VM(Master, Node 등)은 같은 네트워크 대역에 있게 됩니다. 즉, 서로 자유롭게 통신이 가능하면서 동시에 외부 인터넷도 쓸 수 있는 상태가 됩니다.

## 요약

| 구분 | 역할 | 비유 |
| :---: | :---: | :---: |
| NAT | 통신 방식(기술) | 외부와 통신하기 위한 통로와 규칙 |
| VMnet8 | 가상 네트워크 환경(장치) | VM들이 꽃혀 있는 가상의 공유기/허브 |