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

2. 네트워크 관련 정보 확인

```linux
oot@master:~# ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:d3:4e:72 brd ff:ff:ff:ff:ff:ff
    altname enp3s0
    altname enx000c29d34e72
    inet 192.168.2.60/24 brd 192.168.2.255 scope global noprefixroute ens160
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fed3:4e72/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
root@master:~
```

```linux
root@master:~# ip route
default via 192.168.2.254 dev ens160 proto static metric 100 
192.168.2.0/24 dev ens160 proto kernel scope link src 192.168.2.60 metric 100 
root@master:~# 
```

```linux
oot@master:~# ping -c 3 168.126.63.1
PING 168.126.63.1 (168.126.63.1) 자료의 56(84) 바이트.
64 바이트 (168.126.63.1에서): icmp_seq=1 ttl=128 시간=6.18 ms
64 바이트 (168.126.63.1에서): icmp_seq=2 ttl=128 시간=6.33 ms

--- 168.126.63.1 ping 통계 ---
2 패킷이 전송됨, 2 수신됨, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 6.180/6.257/6.334/0.077 ms
root@master:~# 
```

```linux
oot@master:~# nslookup www.google.com
Server:		168.126.63.1
Address:	168.126.63.1#53

Non-authoritative answer:
Name:	www.google.com
Address: 142.250.199.132
Name:	www.google.com
Address: 2404:6800:4004:81a::2004

root@master:~#
```

만약에 위와 같은 ping 테스트 및 nslookup 테스트가 안될 경우, 'nm-connection-editor &'을 실행하여 ip 주소 설정 내용을 확인하고 재설정합니다.

이더넷 → ens160 → IPv4 설정 → IP 주소 관련 설정 → 저장 → 닫기

root@master:~# nmcli connection up ens160
root@master:~# systemctl restart NetworkManager

```linux
root@master:~# wget -q https://raw.githubusercontent.com/ncs10322/kube/main/ping.sh
```

```sh
// 다운받은 ping.sh 내용
cat << EOF > ip_list.txt
168.126.63.1
www.google.com
EOF

sleep 3
printf "\n"
cat ip_list.txt | while read IP_ADDRESS
do
    ping -c 1 -W 1 "$IP_ADDRESS" > /dev/null
    if [ $? -eq 0 ]; then
    echo "\"$IP_ADDRESS\" 으로 통신이 가능합니다."
    else
    echo "\"$IP_ADDRESS\" 으로 통신이 불가능합니다."
    fi
done

printf "\n"
printf "①  네트워크 통신이 불가능하다면, nm-connection-editor & 을 실행하여 IP 주소 설정 내용을 검토 및 수정합니다.\n"
printf "②  검토 및 수정이 완료되었다면, nmcli connection up ens33 && systemctl restart NetworkManager 를 실행합니다.\n"
printf "③  sh ping.sh 를 실행하여 네트워크 통신 테스트를 다시 시작합니다.\n"
printf "④  네트워크 통신이 가능하다면, sh setup.sh 를 실행하여 CentOS 기본 환경 구성을 시작합니다.\n"
printf "\n"
root@master:~# 
```

```linux
wget -q https://raw.githubusercontent.com/ncs10322/kube/main/setup.sh
```

```sh
#!/bin/bash
printf "======================================\n"
printf "  CentOS 기본 환경 구성을 시작합니다. \n"
printf "======================================\n"
printf "\n"
printf "①  SELinux 모드를 permissive 으로 변경합니다.\n"
setenforce 0
sed -i 's/SELINUX=enforcing/SELINUX=permissive/g' /etc/selinux/config
printf "\n"
sleep 3
printf "②  firewalld 방화벽 기능을 해지합니다.\n"
systemctl disable --now firewalld
printf "\n"
sleep 3
printf "③  'httpd', 'gnome-tweaks', 'gnome-extensions-app' 패키지를 설치하고 윈도우 환경을 설정합니다.\n"
yum -y -q install httpd gnome-tweaks gnome-extensions-app.x86_64 && sleep 5
gnome-extensions enable background-logo@fedorahosted.org
gnome-extensions enable places-menu@gnome-shell-extensions.gcampax.github.com
gnome-extensions enable apps-menu@gnome-shell-extensions.gcampax.github.com
gnome-extensions enable window-list@gnome-shell-extensions.gcampax.github.com
gnome-extensions enable desktop-icons@gnome-shell-extensions.gcampax.github.com
gnome-extensions enable launch-new-instance@gnome-shell-extensions.gcampax.github.com
printf "\n"
sleep 3
printf "④  절전 모드 및 화면 잠금 기능을 해지합니다.\n"
systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
gsettings set org.gnome.desktop.session idle-delay 0
gsettings set org.gnome.desktop.screensaver lock-enabled false
gsettings set org.gnome.desktop.screensaver lock-delay 0
printf "\n"
sleep 3
printf "⑤  '.vimrc' 파일을 생성합니다.\n"

cat << EOF > ~/.vimrc
set nu
set ts=4
set title
set paste
set bg=dark
syntax on
autocmd FileType yaml setlocal ai nu ts=2 sw=2 et paste
autocmd Filetype python setlocal ai nu ts=2 sw=2 et paste
EOF

printf "\n"
sleep 3
cp .vimrc /etc/skel/
printf "⑥  '.bashrc' 파일에 alias와 PS1 환경 변수를 설정합니다.\n"

cat << EOF >> .bashrc 
alias vi='vim'
alias ls='ls --color=auto --time-style=long-iso'
export PS1='\[\e[31;1m\][\u@\h\[\e[31;1m\] \w]# \[\e[m\]'
EOF

printf "\n"
sleep 3
printf "⑦  바탕화면에 터미널 아이콘을 생성합니다.\n"
ln -s /usr/bin/gnome-terminal /root/바탕화면/터미널
printf "\n"
sleep 3
printf "⑧  /etc/hosts 파일에 설정을 추가합니다.\n"

cat << EOF >> /etc/hosts 
192.168.2.60  master.example.com  master
192.168.2.61  node1.example.com  node1
192.168.2.62  node2.example.com  node2
192.168.2.63  node3.example.com  node3
192.168.2.80  loadbalancer.examlple.com  loadbalancer
EOF

printf "\n"
sleep 3
printf "⑨  nmap을 설치합니다.\n"
yum -y -q install nmap

printf "\n"
echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config
systemctl restart sshd
sleep 3
printf "======================================\n"
printf "CentOS 기본 환경 구성이 완료되었습니다.\n"
printf "======================================\n"
printf "\n"
sleep 3
cd
printf "⑩  sestatus 를 실시하여 SElinux 모드가 permissive 로 변경되었는지 확인하세요.\n"
printf "⑪  systemctl status firewalld 를 실시하여 방화벽 상태가 inactive 으로 변경되었는지 확인하세요.\n"
printf "⑫  cat .vimrc 를 실시하여

 set 설정 내용이 저장되었는지 확인하세요.\n"
printf "⑬  cat .bashrc 를 실시하여 alias 설정과 PS1 환경 변수 내용이 저장되었는지 확인하세요.\n"
printf "⑭  확인이 완료되었다면, init 0 명령을 실시하여 시스템을 정상 종료하세요.\n"
printf "\n"
root@master:~# 
```

```sh
oot@master:~# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive -> Selinux 특정 패킷 차단 해제
Mode from config file:          permissive -> Selinux 특정 패킷 차단 해제
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      33
root@master:~# 
```

```sh
root@master:~# systemctl status firewalld
○ firewalld.service - firewalld - dynamic firewall daemon
     Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; preset: enabled) -> disabled는 방화벽 차단
     Active: inactive (dead) -> 현재 방화벽 차단
       Docs: man:firewalld(1)

 1월 29 11:16:15 master.example.com systemd[1]: Starting firewalld.service - firewalld - dynamic firewal>
 1월 29 11:16:17 master.example.com systemd[1]: Started firewalld.service - firewalld - dynamic firewall>
 1월 29 11:54:47 master.example.com systemd[1]: Stopping firewalld.service - firewalld - dynamic firewal>
 1월 29 11:54:48 master.example.com systemd[1]: firewalld.service: Deactivated successfully.
 1월 29 11:54:48 master.example.com systemd[1]: Stopped firewalld.service - firewalld - dynamic firewall>
 1월 29 11:54:48 master.example.com systemd[1]: firewalld.service: Consumed 1.386s CPU time, 44.6M memor>
lines 1-11/11 (END)
```

```sh
oot@master:~# cat .vimrc
set nu
set ts=4
set title
set paste
set bg=dark
syntax on
autocmd FileType yaml setlocal ai nu ts=2 sw=2 et paste
autocmd Filetype python setlocal ai nu ts=2 sw=2 et paste
root@master:~# 
```

```sh
root@master:~# cat /etc/hosts
# Loopback entries; do not change.
# For historical reasons, localhost precedes localhost.localdomain:
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
# See hosts(5) for proper format and other examples:
# 192.168.1.10 foo.example.org foo
# 192.168.1.13 bar.example.org bar
192.168.2.60  master.example.com  master
192.168.2.61  node1.example.com  node1
192.168.2.62  node2.example.com  node2
192.168.2.63  node3.example.com  node3
192.168.2.80  loadbalancer.examlple.com  loadbalancer
root@master:~# 
```

```bash
// node1, node2, node3, loadBalancer를 각각 master로 부터 clone하고 hostname을 변경한다.

// node1
root@master:~# hostnamectl set-hostname node1.example.com
// node2
root@master:~# hostnamectl set-hostname node2.example.com
// node3
root@master:~# hostnamectl set-hostname node3.example.com
// node4
root@master:~# hostnamectl set-hostname node4.example.com
// loadbalancer
root@master:~# hostnamectl set-hostname loadbalancer.example.com

// 각각 node1 ~ 3, loadbalancer 까지 ip 주소 끝자리를 61,62,63,80으로 맞춘다
root@master:~#  nmcli connection up ens160 // 맞춘 내용을 적용
```

3. SSH키 생성 및 배포

앤서블과 'kubespray'를 이용하여 쿠버네티스를 설치할 예정이므로 master에서 SSH 키 생성을 생성하여 master를 포함한 node1~node3에 SSH키를 배포한다.

> master에서 SSH키 생성 및 배포

```bash
[root@master ~]# ssh-keygen
[root@master ~]# cat /root/.ssh/id_rsa
```