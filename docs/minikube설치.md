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

[root@master ~]# minikube start --force
😄  Centos 10 의 minikube v1.37.0
❗  minikube skips various validations when --force is supplied; this may lead to unexpected behavior
✨  자동적으로 docker 드라이버가 선택되었습니다. 다른 드라이버 목록: podman, ssh, none
🛑  The "docker" driver should not be used with root privileges. If you wish to continue as root, use --force.
💡  If you are running minikube within a VM, consider using --driver=none:
📘    https://minikube.sigs.k8s.io/docs/reference/drivers/none/

🧯  The requested memory allocation of 3072MiB does not leave room for system overhead (total system memory: 3573MiB). You may face stability issues.
💡  권장: Start minikube with less memory allocated: 'minikube start --memory=3072mb'

📌  Docker 드라이버를 루트 권한으로 사용 중
👍  "minikube" 클러스터의 "minikube" primary control-plane 노드를 시작하는 중
🚜  기본 이미지 v0.0.48를 가져오는 중 ...
💾  쿠버네티스 v1.34.0 을 다운로드 중 ...
    > gcr.io/k8s-minikube/kicbase...:  488.51 MiB / 488.52 MiB  100.00% 5.37 Mi
    > preloaded-images-k8s-v18-v1...:  337.07 MiB / 337.07 MiB  100.00% 3.01 Mi
🔥  docker container (CPUs=2, 메모리=3072MB) 를 생성하는 중 ...
🐳  쿠버네티스 v1.34.0 을 Docker 28.4.0 런타임으로 설치하는 중
🔗  bridge CNI (Container Networking Interface) 를 구성하는 중 ...
🔎  Kubernetes 구성 요소를 확인...
    ▪ 이미지 gcr.io/k8s-minikube/storage-provisioner:v5 사용 중
🌟  애드온 활성화 : storage-provisioner, default-storageclass
💡  kubectl 을 찾을 수 없습니다. 만약 필요하다면, 'minikube kubectl -- get pods -A'를 시도합니다.
🏄  끝났습니다! kubectl이 "minikube" 클러스터와 "default" 네임스페이스를 기본적으로 사용하도록 구성되었습니다
[root@master ~]# 
```

kubectl 을 찾을 수 없습니다. 만약 필요하다면, 'minikube kubectl -- get pods -A'를 시도합니다.
🏄  끝났습니다! kubectl이 "minikube" 클러스터와 "default" 네임스페이스를 기본적으로 사용하도록 구성되었습니다

위의 부분에서 kubectl 을 수동으로 설치해야 한다.

google에서 다음과 같이 검색한다.

- kubectl download 

3. kubectl 설치

```bash
[root@master ~]# curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
[root@master ~]# sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
[root@master ~]# ls -al /usr/local/bin/kubectl
-rwxr-xr-x. 1 root root 58597560 2026-01-29 16:30 /usr/local/bin/kubectl
[root@master ~]# kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null

[root@master ~]# kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   11m   v1.34.0
[root@master ~]#
[root@master ~]# kubectl get nodes -o wide
NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION           CONTAINER-RUNTIME
minikube   Ready    control-plane   13m   v1.34.0   192.168.49.2   <none>        Ubuntu 22.04.5 LTS   6.12.0-191.el10.x86_64   docker://28.4.0
[root@master ~]#
[root@master ~]# kubectl get pod -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
kube-system   coredns-66bc5c9577-2rhqw           1/1     Running   0             13m
kube-system   etcd-minikube                      1/1     Running   0             13m
kube-system   kube-apiserver-minikube            1/1     Running   0             13m
kube-system   kube-controller-manager-minikube   1/1     Running   0             13m
kube-system   kube-proxy-jn8fq                   1/1     Running   0             13m
kube-system   kube-scheduler-minikube            1/1     Running   0             13m
kube-system   storage-provisioner                1/1     Running   1 (13m ago)   13m
[root@master ~]#

```

위에서 control-plane은 명령은 내리는 놈이다.

kube-apiserver-minikube는 전체적인 동작을 담당하는 API 서버이다.

kube-scheduler-minikube는 Pod를 어떤 노드에 생성할 지 조율하는 역할이다.

etcd-minikube는 모든 설정정보를 저장하는 저장소이다.

```bash
[root@master ~]# kubectl get namespaces
NAME              STATUS   AGE
default           Active   17m
kube-node-lease   Active   17m
kube-public       Active   17m
kube-system       Active   17m
[root@master ~]# kubectl get service
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   17m
[root@master ~]#

[root@master ~]# minikube dashboard
* 대시보드를 활성화하는 중 ...
  - 이미지 docker.io/kubernetesui/dashboard:v2.7.0 사용 중
  - 이미지 docker.io/kubernetesui/metrics-scraper:v1.0.8 사용 중
* Some dashboard features require the metrics-server addon. To enable all features please run:

        minikube addons enable metrics-server

* Dashboard 의 상태를 확인 중입니다 ...
* 프록시를 시작하는 중 ...
* Proxy 의 상태를 확인 중입니다 ...
http://127.0.0.1:34805/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
^C
[root@master ~]#

```

wmware의 master에서 firefox 를 열고 다음 url을 호출한다. dashboard가 열린다.

http://127.0.0.1:34805/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/


```bash
[root@master ~]# kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.5
deployment.apps/hello-minikube created
[root@master ~]#
[root@master ~]# kubectl get deploy,pod
NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-minikube   1/1     1            1           97s

NAME                                  READY   STATUS    RESTARTS   AGE
pod/hello-minikube-5458498444-hqfbp   1/1     Running   0          97s
[root@master ~]#
[root@master ~]# kubectl describe deploy hello-minikube
Name:                   hello-minikube
Namespace:              default
CreationTimestamp:      Thu, 29 Jan 2026 16:49:17 +0900
Labels:                 app=hello-minikube
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=hello-minikube
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=hello-minikube
  Containers:
   echoserver:
    Image:         k8s.gcr.io/echoserver:1.5
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   hello-minikube-5458498444 (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m21s  deployment-controller  Scaled up replica set hello-minikube-5458498444 from 0 to 1
[root@master ~]#
[root@master ~]# kubectl get deploy,rs,pod
NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-minikube   1/1     1            1           4m41s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-minikube-5458498444   1         1         1       4m41s

NAME                                  READY   STATUS    RESTARTS   AGE
pod/hello-minikube-5458498444-hqfbp   1/1     Running   0          4m41s
[root@master ~]#

[root@master ~]# kubectl describe rs hello-minikube-5458498444
Name:           hello-minikube-5458498444
Namespace:      default
Selector:       app=hello-minikube,pod-template-hash=5458498444
Labels:         app=hello-minikube
                pod-template-hash=5458498444
Annotations:    deployment.kubernetes.io/desired-replicas: 1
                deployment.kubernetes.io/max-replicas: 2
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/hello-minikube
Replicas:       1 current / 1 desired
Pods Status:    1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=hello-minikube
           pod-template-hash=5458498444
  Containers:
   echoserver:
    Image:         k8s.gcr.io/echoserver:1.5
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  5m12s  replicaset-controller  Created pod: hello-minikube-5458498444-hqfbp
[root@master ~]#
[root@master ~]# kubectl get pod -o wide
NAME                              READY   STATUS    RESTARTS   AGE    IP           NODE       NOMINATED NODE   READINESS GATES
hello-minikube-5458498444-hqfbp   1/1     Running   0          6m9s   10.244.0.5   minikube   <none>           <none>
[root@master ~]#
[root@master ~]# kubectl expose deployment hello-minikube --type=NodePort --port=8080
service/hello-minikube exposed
[root@master ~]# kubectl get services
NAME             TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-minikube   NodePort    10.98.108.86   <none>        8080:31912/TCP   7s
kubernetes       ClusterIP   10.96.0.1      <none>        443/TCP          32m
[root@master ~]#
[root@master ~]# minikube service hello-minikube
┌───── ┬────────┬────── ┬───────────── ┐
│ NAMESPACE │      NAME      │ TARGET PORT │            URL            │
├───── ┼────────┼────── ┼───────────── ┤

│ default   │ hello-minikube │ 8080        │ http://192.168.49.2:31912 │
└───── ┴────────┴────── ┴───────────── ┘
* Opening service default/hello-minikube in default browser...
  http://192.168.49.2:31912
[root@master ~]# kubectl port-forward services/hello-minikube 8000:8080
Forwarding from 127.0.0.1:8000 -> 8080
Forwarding from [::1]:8000 -> 8080

```

```bash
[root@master ~]# kubectl expose deployment loadbalancer --type=LoadBalancer --port=8080
service/loadbalancer exposed
[root@master ~]# 
[root@master ~]# kubectl get services
NAME             TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-minikube   NodePort       10.98.108.86     <none>        8080:31912/TCP   7m55s
kubernetes       ClusterIP      10.96.0.1        <none>        443/TCP          40m
loadbalancer     LoadBalancer   10.109.187.158   <pending>     8080:32242/TCP   53s
[root@master ~]#
root@master ~]# kubectl get service
NAME             TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)          AGE
hello-minikube   NodePort       10.98.108.86     <none>           8080:31912/TCP   10m
kubernetes       ClusterIP      10.96.0.1        <none>           443/TCP          42m
loadbalancer     LoadBalancer   10.109.187.158   10.109.187.158   8080:32242/TCP   3m2s
[root@master ~]# minikube pause
⏸️  Pausing node minikube ... 
⏯️  Paused 18 containers in: kube-system, kubernetes-dashboard, storage-gluster, istio-operator
[root@master ~]# kubectl get service
Unable to connect to the server: net/http: TLS handshake timeout
[root@master ~]# minikube status
minikube
type: Control Plane
host: Running
kubelet: Stopped
apiserver: Paused
kubeconfig: Configured

[root@master ~]# minikube unpause
⏸️  Unpausing node minikube ... 
⏸️  Unpaused 18 containers in: kube-system, kubernetes-dashboard, storage-gluster, istio-operator
[root@master ~]#
[root@master ~]# minikube delete --all
🔥  docker 의 "minikube" 를 삭제하는 중 ...
🔥  /root/.minikube/machines/minikube 제거 중 ...
💀  "minikube" 클러스터 관련 정보가 모두 삭제되었습니다
🔥  모든 프로필이 성공적으로 삭제되었습니다
[root@master ~]# minikube status
🤷  Profile "minikube" not found. Run "minikube profile list" to view all profiles.
👉  To start a cluster, run: "minikube start"
[root@master ~]# minikube start --force
😄  Centos 10 의 minikube v1.37.0
❗  minikube skips various validations when --force is supplied; this may lead to unexpected behavior
🎉  minikube 1.38.0 이 사용가능합니다! 다음 경로에서 다운받으세요: https://github.com/kubernetes/minikube/releases/tag/v1.38.0
💡  해당 알림을 비활성화하려면 다음 명령어를 실행하세요. 'minikube config set WantUpdateNotification false'
✨  자동적으로 docker 드라이버가 선택되었습니다. 다른 드라이버 목록: podman, none, ssh
🛑  The "docker" driver should not be used with root privileges. If you wish to continue as root, use --force.
💡  If you are running minikube within a VM, consider using --driver=none:
📘    https://minikube.sigs.k8s.io/docs/reference/drivers/none/

🧯  The requested memory allocation of 3072MiB does not leave room for system overhead (total system memory: 3573MiB). You may face stability issues.
💡  권장: Start minikube with less memory allocated: 'minikube start --memory=3072mb'

📌  Docker 드라이버를 루트 권한으로 사용 중
👍  "minikube" 클러스터의 "minikube" primary control-plane 노드를 시작하는 중
🚜  기본 이미지 v0.0.48를 가져오는 중 ...
🔥  docker container (CPUs=2, 메모리=3072MB) 를 생성하는 중 ...
🐳  쿠버네티스 v1.34.0 을 Docker 28.4.0 런타임으로 설치하는 중
🔗  bridge CNI (Container Networking Interface) 를 구성하는 중 ...
🔎  Kubernetes 구성 요소를 확인...
    ▪ 이미지 gcr.io/k8s-minikube/storage-provisioner:v5 사용 중
🌟  애드온 활성화 : storage-provisioner, default-storageclass
🏄  끝났습니다! kubectl이 "minikube" 클러스터와 "default" 네임스페이스를 기본적으로 사용하도록 구성되었습니다
[root@master ~]# docker image ls
                                                                      i Info →   U  In Use
IMAGE                                      ID             DISK USAGE   CONTENT SIZE   EXTRA
gcr.io/k8s-minikube/kicbase:v0.0.48        41454ef774d0       1.84GB          512MB        
gcr.io/k8s-minikube/kicbase@sha256:7171c97a51623558720f8e5878e4f4637da093e2f2ed589997bedc6c1549b2b1
                                           7171c97a5162       1.84GB          512MB    U   
[root@master ~]# kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   63s   v1.34.0
[root@master ~]# kubectl get node -o wide
NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION           CONTAINER-RUNTIME
minikube   Ready    control-plane   69s   v1.34.0   192.168.49.2   <none>        Ubuntu 22.04.5 LTS   6.12.0-191.el10.x86_64   docker://28.4.0
[root@master ~]# kubectl get pod -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
kube-system   coredns-66bc5c9577-f5qwt           1/1     Running   0             66s
kube-system   etcd-minikube                      1/1     Running   0             71s
kube-system   kube-apiserver-minikube            1/1     Running   0             71s
kube-system   kube-controller-manager-minikube   1/1     Running   0             73s
kube-system   kube-proxy-trfxc                   1/1     Running   0             66s
kube-system   kube-scheduler-minikube            1/1     Running   0             71s
kube-system   storage-provisioner                1/1     Running   1 (34s ago)   69s
[root@master ~]# 
[root@master ~]# minikube stop
✋  "minikube" 노드를 중지하는 중 ...
🛑  "minikube"를 SSH로 전원을 끕니다 ...
🛑  1개의 노드가 중지되었습니다.
[root@master ~]# 
```

결론적으로 minikube는 사용하지 않는다.

우리는 이미 vmware로 부터 각기 master, node1, node2, node3, loadbalancer를 물리적으로 생성했기 때문이다.

