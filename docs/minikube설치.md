# minikube 설치

minikube는 로컬 시스템에서 master와 node를 동시에 구현할 수 있기 때문에 Kubernetes 기능 및 구성을 테스트 할 수 있다. 

master에 'minikube'를 설치하여 간단한 Kubernetes 테스트를 진행한다.

1. 도커 설치

```bash
yum -y remove runc && \
curl -fsSL https://get.docker.com -o get-docker.sh && \
sh get-docker.sh && \
systemctl enable --now docker
```

2. minikube 설치

```bash
[root@master ~]# curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64

[root@master ~]# sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64 // 설치 완료 후 삭제:y

[root@master ~]# ls -l /usr/local/bin/minikube
-rwxr-xr-x. 1 root root 139886451 2026-01-29 16:18 /usr/local/bin/minikube
```