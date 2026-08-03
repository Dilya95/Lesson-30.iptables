# Домашнее задание 30: Сценарии iptables

## Задания
реализовать knocking port<br>
centralRouter может попасть на ssh inetrRouter через knock скрипт
пример в материалах.<br>
добавить inetRouter2, который виден(маршрутизируется (host-only тип сети для виртуалки)) с хоста или форвардится порт через локалхост.<br>
запустить nginx на centralServer.<br>
пробросить 80й порт на inetRouter2 8080.<br>
дефолт в инет оставить через inetRouter.<br>
Реализовать проход на 80й порт без маскарадинга*


## Структура
├── README.md<br>
└── Vagrantfile.

## Выполнение

### Реализация knocking port. centralRouter может попасть на ssh inetrRouter через knock скрипт
```

[root@otus-homework otus-linux]# vagrant ssh centralRouter
Welcome to Ubuntu 26.04 LTS (GNU/Linux 7.0.0-22-generic x86_64)

 * Documentation:  https://docs.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Fri Jul 31 01:08:00 PM UTC 2026

  System load:             0.15
  Usage of /:              16.1% of 29.82GB
  Memory usage:            33%
  Swap usage:              0%
  Processes:               103
  Users logged in:         0
  IPv4 address for enp0s3: 10.0.2.15
  IPv6 address for enp0s3: fd17:625c:f037:2:a00:27ff:fe0a:4e27
  IPv4 address for eth1:   192.168.255.2


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.
Last login: Wed Jul 29 10:20:48 2026 from 10.0.2.2
vagrant@centralRouter:~$ nc -zv -w 2 192.168.255.1 22
Connection to 192.168.255.1 22 port [tcp/ssh] succeeded!
vagrant@centralRouter:~$ ip -br address
lo               UNKNOWN        127.0.0.1/8 ::1/128 
enp0s3           UP             10.0.2.15/24 metric 100 fd17:625c:f037:2:a00:27ff:fe0a:4e27/64 fe80::a00:27ff:fe0a:4e27/64 
eth1             UP             192.168.255.2/30 fe80::a00:27ff:fe1d:a865/64 
eth2             UP             192.168.0.1/28 fe80::a00:27ff:fe7d:bb70/64 
eth3             UP             192.168.0.33/28 fe80::a00:27ff:fe4e:1690/64 
eth4             UP             192.168.0.65/26 fe80::a00:27ff:fee3:2ec5/64 
eth5             UP             192.168.255.9/30 fe80::a00:27ff:fe07:7965/64 
eth6             UP             192.168.255.5/30 fe80::a00:27ff:fea9:1a96/64 
eth7             UP             192.168.50.11/24 fe80::a00:27ff:fe06:a09c/64 
vagrant@centralRouter:~$ ip route
default via 192.168.255.1 dev eth1 proto static 
2.144.48.252 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100 
10.0.2.2 dev enp0s3 proto dhcp scope link src 10.0.2.15 metric 100 
45.144.48.252 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
45.144.48.253 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
192.168.0.0/28 dev eth2 proto kernel scope link src 192.168.0.1 
192.168.0.32/28 dev eth3 proto kernel scope link src 192.168.0.33 
192.168.0.64/26 dev eth4 proto kernel scope link src 192.168.0.65 
192.168.1.0/24 via 192.168.255.6 dev eth6 proto static 
192.168.2.0/24 via 192.168.255.10 dev eth5 proto static 
192.168.50.0/24 dev eth7 proto kernel scope link src 192.168.50.11 
192.168.255.0/30 dev eth1 proto kernel scope link src 192.168.255.2 
192.168.255.4/30 dev eth6 proto kernel scope link src 192.168.255.5 
192.168.255.8/30 dev eth5 proto kernel scope link src 192.168.255.9 
vagrant@centralRouter:~$ exit
logout
Connection to 127.0.0.1 closed.
[root@otus-homework otus-linux]# vagrant ssh centralServer
Welcome to Ubuntu 26.04 LTS (GNU/Linux 7.0.0-22-generic x86_64)

 * Documentation:  https://docs.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Fri Jul 31 01:12:57 PM UTC 2026

  System load:             0.29
  Usage of /:              16.1% of 29.82GB
  Memory usage:            32%
  Swap usage:              0%
  Processes:               104
  Users logged in:         0
  IPv4 address for enp0s3: 10.0.2.15
  IPv6 address for enp0s3: fd17:625c:f037:2:a00:27ff:fe0a:4e27
  IPv4 address for eth1:   192.168.0.2


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.
Last login: Wed Jul 29 10:20:59 2026 from 10.0.2.2
vagrant@centralServer:~$ ip -br address
lo               UNKNOWN        127.0.0.1/8 ::1/128 
enp0s3           UP             10.0.2.15/24 metric 100 fd17:625c:f037:2:a00:27ff:fe0a:4e27/64 fe80::a00:27ff:fe0a:4e27/64 
eth1             UP             192.168.0.2/28 fe80::a00:27ff:fe5a:a12a/64 
eth2             UP             192.168.50.12/24 fe80::a00:27ff:fe67:2ab0/64 
vagrant@centralServer:~$ ip route
default via 192.168.0.1 dev eth1 proto static 
2.144.48.252 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100 
10.0.2.2 dev enp0s3 proto dhcp scope link src 10.0.2.15 metric 100 
45.144.48.252 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
45.144.48.253 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
192.168.0.0/28 dev eth1 proto kernel scope link src 192.168.0.2 
192.168.50.0/24 dev eth2 proto kernel scope link src 192.168.50.12 
vagrant@centralServer:~$ 


[root@otus-homework otus-linux]# vagrant ssh inetRouter
Welcome to Ubuntu 26.04 LTS (GNU/Linux 7.0.0-22-generic x86_64)
vagrant@inetRouter:~$ sudo -i

root@inetRouter:~# sudo iptables -L INPUT -n -v --line-numbers
Chain INPUT (policy ACCEPT 1276 packets, 260K bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           
2       36  5916 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
3        2   104 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22

root@inetRouter:~# iptables-save
# Generated by iptables-save v1.8.11 (nf_tables) on Fri Jul 31 13:14:52 2026
*filter
:INPUT ACCEPT [1288:260965]
:FORWARD ACCEPT [4280:864772]
:OUTPUT ACCEPT [1287:262546]
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
COMMIT
# Completed on Fri Jul 31 13:14:52 2026
# Generated by iptables-save v1.8.11 (nf_tables) on Fri Jul 31 13:14:52 2026
*nat
:PREROUTING ACCEPT [1940:416328]
:INPUT ACCEPT [24:12776]
:OUTPUT ACCEPT [1126:243310]
:POSTROUTING ACCEPT [10:680]
-A POSTROUTING ! -d 192.168.0.0/16 -o enp0s3 -j MASQUERADE
COMMIT
# Completed on Fri Jul 31 13:14:52 2026


root@inetRouter:~# iptables-save > /root/iptables-before-knocking.rules

root@inetRouter:~# ls -l /root/iptables-before-knocking.rules
-rw-r--r-- 1 root root 640 Jul 31 13:17 /root/iptables-before-knocking.rules

root@inetRouter:~# modprobe xt_recent

root@inetRouter:~# lsmod | grep xt_recent
xt_recent              24576  0
x_tables               65536  5 xt_conntrack,nft_compat,xt_tcpudp,xt_recent,xt_MASQUERADE


root@inetRouter:~# iptables -N KNOCKING

root@inetRouter:~# iptables -N KNOCK_STEP2

root@inetRouter:~# iptables -N KNOCK_STEP3

root@inetRouter:~# iptables -L -n
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         

Chain KNOCKING (0 references)
target     prot opt source               destination         

Chain KNOCK_STEP2 (0 references)
target     prot opt source               destination         

Chain KNOCK_STEP3 (0 references)
target     prot opt source               destination   


root@inetRouter:~# iptables -A KNOCKING \
  -p tcp --dport 8881 \
  -m recent --name KNOCK1 --set \
  -j DROP


root@inetRouter:~# iptables -A KNOCKING \
  -p tcp --dport 7777 \
  -m recent --name KNOCK1 --rcheck --seconds 15 \
  -j KNOCK_STEP2


root@inetRouter:~# iptables -A KNOCK_STEP2 \
  -m recent --name KNOCK2 --set \
  -j DROP


root@inetRouter:~# iptables -A KNOCKING \
  -p tcp --dport 9991 \
  -m recent --name KNOCK2 --rcheck --seconds 15 \
  -j KNOCK_STEP3


root@inetRouter:~# iptables -A KNOCK_STEP3 \
  -m recent --name SSH_ALLOWED --set \
  -j DROP


root@inetRouter:~# iptables -A KNOCKING \
  -p tcp --dport 22 \
  -m recent --name SSH_ALLOWED --rcheck --seconds 30 \
  -j ACCEPT


root@inetRouter:~# iptables -A KNOCKING \
  -p tcp \
  -j DROP


root@inetRouter:~# iptables -I INPUT 1 \
  -s 192.168.255.2 \
  -d 192.168.255.1 \
  -p tcp \
  -m conntrack --ctstate NEW \
  -j KNOCKING


root@inetRouter:~# iptables -L INPUT -n -v --line-numbers
Chain INPUT (policy ACCEPT 1464 packets, 270K bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 KNOCKING   tcp  --  *      *       192.168.255.2        192.168.255.1        ctstate NEW
2        0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           
3       36  5916 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
4        2   104 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22


root@inetRouter:~# iptables -L KNOCKING -n -v --line-numbers
Chain KNOCKING (1 references)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 DROP       tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:8881 recent: SET name: KNOCK1 side: source mask: 255.255.255.255
2        0     0 KNOCK_STEP2  tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:7777 recent: CHECK seconds: 15 name: KNOCK1 side: source mask: 255.255.255.255
3        0     0 KNOCK_STEP3  tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:9991 recent: CHECK seconds: 15 name: KNOCK2 side: source mask: 255.255.255.255
4        0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:22 recent: CHECK seconds: 30 name: SSH_ALLOWED side: source mask: 255.255.255.255
5        0     0 DROP       tcp  --  *      *       0.0.0.0/0            0.0.0.0/0   


root@inetRouter:~# iptables -L KNOCK_STEP2 -n -v --line-numbers
Chain KNOCK_STEP2 (1 references)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0            recent: SET name: KNOCK2 side: source mask: 255.255.255.255


root@inetRouter:~# iptables -L KNOCK_STEP3 -n -v --line-numbers
Chain KNOCK_STEP3 (1 references)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0            recent: SET name: SSH_ALLOWED side: source mask: 255.255.255.255


root@inetRouter:~# iptables-save
# Generated by iptables-save v1.8.11 (nf_tables) on Fri Jul 31 13:20:42 2026
*filter
:INPUT ACCEPT [1515:272549]
:FORWARD ACCEPT [4288:866500]
:OUTPUT ACCEPT [1474:279058]
:KNOCKING - [0:0]
:KNOCK_STEP2 - [0:0]
:KNOCK_STEP3 - [0:0]
-A INPUT -s 192.168.255.2/32 -d 192.168.255.1/32 -p tcp -m conntrack --ctstate NEW -j KNOCKING
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A KNOCKING -p tcp -m tcp --dport 8881 -m recent --set --name KNOCK1 --mask 255.255.255.255 --rsource -j DROP
-A KNOCKING -p tcp -m tcp --dport 7777 -m recent --rcheck --seconds 15 --name KNOCK1 --mask 255.255.255.255 --rsource -j KNOCK_STEP2
-A KNOCKING -p tcp -m tcp --dport 9991 -m recent --rcheck --seconds 15 --name KNOCK2 --mask 255.255.255.255 --rsource -j KNOCK_STEP3
-A KNOCKING -p tcp -m tcp --dport 22 -m recent --rcheck --seconds 30 --name SSH_ALLOWED --mask 255.255.255.255 --rsource -j ACCEPT
-A KNOCKING -p tcp -j DROP
-A KNOCK_STEP2 -m recent --set --name KNOCK2 --mask 255.255.255.255 --rsource -j DROP
-A KNOCK_STEP3 -m recent --set --name SSH_ALLOWED --mask 255.255.255.255 --rsource -j DROP
COMMIT
# Completed on Fri Jul 31 13:20:42 2026
# Generated by iptables-save v1.8.11 (nf_tables) on Fri Jul 31 13:20:42 2026
*nat
:PREROUTING ACCEPT [1944:417192]
:INPUT ACCEPT [24:12776]
:OUTPUT ACCEPT [1127:243526]
:POSTROUTING ACCEPT [10:680]
-A POSTROUTING ! -d 192.168.0.0/16 -o enp0s3 -j MASQUERADE
COMMIT
# Completed on Fri Jul 31 13:20:42 2026






[root@otus-homework otus-linux]# vagrant ssh centralRouter

root@centralRouter:~$ ip -br address
lo               UNKNOWN        127.0.0.1/8 ::1/128 
enp0s3           UP             10.0.2.15/24 metric 100 fd17:625c:f037:2:a00:27ff:fe0a:4e27/64 fe80::a00:27ff:fe0a:4e27/64 
eth1             UP             192.168.255.2/30 fe80::a00:27ff:fe1d:a865/64 
eth2             UP             192.168.0.1/28 fe80::a00:27ff:fe7d:bb70/64 
eth3             UP             192.168.0.33/28 fe80::a00:27ff:fe4e:1690/64 
eth4             UP             192.168.0.65/26 fe80::a00:27ff:fee3:2ec5/64 
eth5             UP             192.168.255.9/30 fe80::a00:27ff:fe07:7965/64 
eth6             UP             192.168.255.5/30 fe80::a00:27ff:fea9:1a96/64 
eth7             UP             192.168.50.11/24 fe80::a00:27ff:fe06:a09c/64 

root@centralRouter:~$ nc -zv -w 2 192.168.255.1 22
nc: connect to 192.168.255.1 port 22 (tcp) timed out: Operation now in progress



root@centralRouter:~# sudo tee /usr/local/bin/knock-ssh >/dev/null <<'EOF'
#!/usr/bin/env bash

set -euo pipefail

target="${1:-192.168.255.1}"

for port in 8881 7777 9991; do
    nc -z -w 1 "${target}" "${port}" >/dev/null 2>&1 || true
    sleep 1
done

echo "Knock sequence sent to ${target}"
echo "SSH should be available for approximately 30 seconds"
EOF

root@centralRouter:~# sudo chmod 0755 /usr/local/bin/knock-ssh

root@centralRouter:~# cat /usr/local/bin/knock-ssh
#!/usr/bin/env bash

set -euo pipefail

target="${1:-192.168.255.1}"

for port in 8881 7777 9991; do
    nc -z -w 1 "${target}" "${port}" >/dev/null 2>&1 || true
    sleep 1
done

echo "Knock sequence sent to ${target}"
echo "SSH should be available for approximately 30 seconds"

root@centralRouter:~# sudo /usr/local/bin/knock-ssh 192.168.255.1
Knock sequence sent to 192.168.255.1
SSH should be available for approximately 30 seconds





root@centralRouter:~$ nc -zv -w 2 192.168.255.1 22
Connection to 192.168.255.1 22 port [tcp/ssh] succeeded!

root@centralRouter:~$ sleep 35
nc -zv -w 2 192.168.255.1 22
nc: connect to 192.168.255.1 port 22 (tcp) timed out: Operation now in progress

root@inetRouter:~# cp /etc/iptables/rules.v4 /root/rules.v4-knocking-working


root@inetRouter:~$ cat /proc/net/xt_recent/KNOCK1
src=192.168.255.2 ttl: 64 last_seen: 4475122335 oldest_pkt: 1 4475122335

root@inetRouter:~$ cat /proc/net/xt_recent/KNOCK2
src=192.168.255.2 ttl: 64 last_seen: 4475124383 oldest_pkt: 1 4475124383

root@inetRouter:~$ cat /proc/net/xt_recent/SSH_ALLOWED
src=192.168.255.2 ttl: 64 last_seen: 4475126442 oldest_pkt: 1 4475126442

root@inetRouter:~# apt-get update
DEBIAN_FRONTEND=noninteractive apt-get install -y \
  iptables-persistent \
  netfilter-persistent


root@inetRouter:~# netfilter-persistent save
run-parts: executing /usr/share/netfilter-persistent/plugins.d/15-ip4tables save
run-parts: executing /usr/share/netfilter-persistent/plugins.d/25-ip6tables save


root@inetRouter:~# grep -nE 'KNOCK|SSH_ALLOWED|8881|7777|9991' /etc/iptables/rules.v4
6::KNOCKING - [0:0]
7::KNOCK_STEP2 - [0:0]
8::KNOCK_STEP3 - [0:0]
9:-A INPUT -s 192.168.255.2/32 -d 192.168.255.1/32 -p tcp -m conntrack --ctstate NEW -j KNOCKING
13:-A KNOCKING -p tcp -m tcp --dport 8881 -m recent --set --name KNOCK1 --mask 255.255.255.255 --rsource -j DROP
14:-A KNOCKING -p tcp -m tcp --dport 7777 -m recent --rcheck --seconds 15 --name KNOCK1 --mask 255.255.255.255 --rsource -j KNOCK_STEP2
15:-A KNOCKING -p tcp -m tcp --dport 9991 -m recent --rcheck --seconds 15 --name KNOCK2 --mask 255.255.255.255 --rsource -j KNOCK_STEP3
16:-A KNOCKING -p tcp -m tcp --dport 22 -m recent --rcheck --seconds 30 --name SSH_ALLOWED --mask 255.255.255.255 --rsource -j ACCEPT
17:-A KNOCKING -p tcp -j DROP
18:-A KNOCK_STEP2 -m recent --set --name KNOCK2 --mask 255.255.255.255 --rsource -j DROP
19:-A KNOCK_STEP3 -m recent --set --name SSH_ALLOWED --mask 255.255.255.255 --rsource -j DROP


root@inetRouter:~# systemctl status netfilter-persistent --no-pager
● netfilter-persistent.service - netfilter persistent configuration
     Loaded: loaded (/usr/lib/systemd/system/netfilter-persistent.service; enabled; preset: enabled)
    Drop-In: /usr/lib/systemd/system/netfilter-persistent.service.d
             └─iptables.conf
     Active: active (exited) since Fri 2026-07-31 13:32:02 UTC; 36s ago
 Invocation: 8a165ba34d374162bb0075f5afb73637
       Docs: man:netfilter-persistent(8)
   Main PID: 6373 (code=exited, status=0/SUCCESS)
   Mem peak: 1.8M
        CPU: 91ms

Jul 31 13:32:02 inetRouter systemd[1]: Starting netfilter-persistent.service - netfilter persistent configuration...
Jul 31 13:32:02 inetRouter netfilter-persistent[6375]: run-parts: executing /usr/share/netfilter-persistent/plugins.d/15-ip4tables start
Jul 31 13:32:02 inetRouter netfilter-persistent[6376]: Warning: skipping IPv4 (no rules to load)
Jul 31 13:32:02 inetRouter netfilter-persistent[6375]: run-parts: executing /usr/share/netfilter-persistent/plugins.d/25-ip6tables start
Jul 31 13:32:02 inetRouter netfilter-persistent[6377]: Warning: skipping IPv6 (no rules to load)
Jul 31 13:32:02 inetRouter netfilter-persistent[6377]: /usr/share/netfilter-persistent/plugins.d/25-ip6tables: 39: cannot open /etc/iptables/r… such file
Jul 31 13:32:02 inetRouter netfilter-persistent[6377]: Error: IPv6 rules failed test load. New rules NOT loaded
Jul 31 13:32:02 inetRouter systemd[1]: Finished netfilter-persistent.service - netfilter persistent configuration.
Hint: Some lines were ellipsized, use -l to show in full.
```

### Добавить inetRouter2, который виден(маршрутизируется) с хоста или форвардится порт через локалхост.<br>
```
[root@otus-homework otus-linux]# cat  Vagrantfile
MACHINES = {
  :inetRouter => {
        :box_name => "bento/ubuntu-26.04",
        :vm_name => "inetRouter",
        #:public => {:ip => "10.10.10.1", :adapter => 1},
        :net => [   
                    #ip, adpter, netmask, virtualbox__intnet
                    ["192.168.255.1", 2, "255.255.255.252",  "router-net"], 
                    ["192.168.50.10", 8, "255.255.255.0"],
                ]
  },

  :centralRouter => {
        :box_name => "bento/ubuntu-26.04",
        :vm_name => "centralRouter",
        :net => [
                   ["192.168.255.2",  2, "255.255.255.252",  "router-net"],
                   ["192.168.0.1",    3, "255.255.255.240",  "dir-net"],
                   ["192.168.0.33",   4, "255.255.255.240",  "hw-net"],
                   ["192.168.0.65",   5, "255.255.255.192",  "mgt-net"],
                   ["192.168.255.9",  6, "255.255.255.252",  "office1-central"],
                   ["192.168.255.5",  7, "255.255.255.252",  "office2-central"],
                   ["192.168.50.11",  8, "255.255.255.0"],
                ]
  },

  :centralServer => {
        :box_name => "bento/ubuntu-26.04",
        :vm_name => "centralServer",
        :net => [
                   ["192.168.0.2",    2, "255.255.255.240",  "dir-net"],
                   ["192.168.50.12",  8, "255.255.255.0"],
                ]
  },

  :office1Router => {
        :box_name => "bento/ubuntu-26.04",
        :vm_name => "office1Router",
        :net => [
                   ["192.168.255.10",  2,  "255.255.255.252",  "office1-central"],
                   ["192.168.2.1",     3,  "255.255.255.192",  "dev1-net"],
                   ["192.168.2.65",    4,  "255.255.255.192",  "test1-net"],
                   ["192.168.2.129",   5,  "255.255.255.192",  "managers-net"],
                   ["192.168.2.193",   6,  "255.255.255.192",  "office1-net"],
                   ["192.168.50.20",   8,  "255.255.255.0"],
                ]
  },

  :office1Server => {
        :box_name => "bento/ubuntu-26.04",
        :vm_name => "office1Server",
        :net => [
                   ["192.168.2.130",  2,  "255.255.255.192",  "managers-net"],
                   ["192.168.50.21",  8,  "255.255.255.0"],
                ]
  },

  :office2Router => {
       :box_name => "bento/ubuntu-26.04",
       :vm_name => "office2Router",
       :net => [
                   ["192.168.255.6",  2,  "255.255.255.252",  "office2-central"],
                   ["192.168.1.1",    3,  "255.255.255.128",  "dev2-net"],
                   ["192.168.1.129",  4,  "255.255.255.192",  "test2-net"],
                   ["192.168.1.193",  5,  "255.255.255.192",  "office2-net"],
                   ["192.168.50.30",  8,  "255.255.255.0"],
               ]
  },

  :office2Server => {
       :box_name => "bento/ubuntu-26.04",
       :vm_name => "office2Server",
       :net => [
                  ["192.168.1.2",    2,  "255.255.255.128",  "dev2-net"],
                  ["192.168.50.31",  8,  "255.255.255.0"],
               ]
  },

  :inetRouter2 => {
       :box_name => "bento/ubuntu-26.04",
       :vm_name => "inetRouter2",
       :net => [
                  ["192.168.255.13", 2, "255.255.255.252", "router-net"],
                  ["192.168.56.20",  3, "255.255.255.0"],
               ]
  }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]
      
      box.vm.provider "virtualbox" do |v|
        v.memory = 768
        v.cpus = 1
       end

      boxconfig[:net].each do |ipconf|
        box.vm.network("private_network", ip: ipconf[0], adapter: ipconf[1], netmask: ipconf[2], virtualbox__intnet: ipconf[3])
      end

      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end

      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
      SHELL
    end
  end
end


[root@otus-homework otus-linux]# vagrant validate
==> inetRouter: You assigned a static IP ending in ".1" or ":1" to this machine.
==> inetRouter: This is very often used by the router and can cause the
==> inetRouter: network to not work properly. If the network doesn't work
==> inetRouter: properly, try changing this IP.
==> centralRouter: You assigned a static IP ending in ".1" or ":1" to this machine.
==> centralRouter: This is very often used by the router and can cause the
==> centralRouter: network to not work properly. If the network doesn't work
==> centralRouter: properly, try changing this IP.
==> office1Router: You assigned a static IP ending in ".1" or ":1" to this machine.
==> office1Router: This is very often used by the router and can cause the
==> office1Router: network to not work properly. If the network doesn't work
==> office1Router: properly, try changing this IP.
==> office2Router: You assigned a static IP ending in ".1" or ":1" to this machine.
==> office2Router: This is very often used by the router and can cause the
==> office2Router: network to not work properly. If the network doesn't work
==> office2Router: properly, try changing this IP.
Vagrantfile validated successfully.





[root@otus-homework otus-linux]# nano /etc/vbox/networks.conf 

[root@otus-homework otus-linux]# cat /etc/vbox/networks.conf 
* 192.168.11.0/24
* 192.168.50.0/24
*  10.0.0.0/16
* 192.168.56.0/24

[root@otus-homework otus-linux]# vagrant up inetRouter2

[root@otus-homework otus-linux]# vagrant status
Current machine states:

inetRouter                running (virtualbox)
centralRouter             running (virtualbox)
centralServer             running (virtualbox)
office1Router             running (virtualbox)
office1Server             running (virtualbox)
office2Router             running (virtualbox)
office2Server             running (virtualbox)
inetRouter2               running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.


[root@otus-homework otus-linux]# vagrant ssh inetRouter2

vagrant@inetRouter2:~$ sudo -i

root@inetRouter2:~# ip -br address
lo               UNKNOWN        127.0.0.1/8 ::1/128 
enp0s3           UP             10.0.2.15/24 metric 100 fd17:625c:f037:2:a00:27ff:fe0a:4e27/64 fe80::a00:27ff:fe0a:4e27/64 
eth1             UP             192.168.255.13/30 fe80::a00:27ff:fe14:16cd/64 
eth2             UP             192.168.56.20/24 fe80::a00:27ff:fe81:9cad/64 
root@inetRouter2:~# ip route
default via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
2.144.48.252 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100 
10.0.2.2 dev enp0s3 proto dhcp scope link src 10.0.2.15 metric 100 
45.144.48.252 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
45.144.48.253 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
192.168.56.0/24 dev eth2 proto kernel scope link src 192.168.56.20 
192.168.255.12/30 dev eth1 proto kernel scope link src 192.168.255.13 
```

### Запустить nginx на centralServer. Пробросить 80й порт на inetRouter2 8080. Дефолт в инет оставить через inetRouter
```
vagrant@centralRouter:~$ sudo -i
root@centralRouter:~# ip -o -4 address show | grep '192.168.255.2/'
3: eth1    inet 192.168.255.2/30 brd 192.168.255.3 scope global eth1\       valid_lft forever preferred_lft forever

root@centralRouter:~# ip address add 192.168.255.14/30 dev eth1

root@centralRouter:~# ip -br address show dev eth1
eth1             UP             192.168.255.2/30 192.168.255.14/30 fe80::a00:27ff:fe1d:a865/64 

root@centralRouter:~# ping -c 4 192.168.255.13
PING 192.168.255.13 (192.168.255.13) 56(84) bytes of data.
64 bytes from 192.168.255.13: icmp_seq=1 ttl=64 time=8.05 ms
64 bytes from 192.168.255.13: icmp_seq=2 ttl=64 time=3.68 ms
^C
--- 192.168.255.13 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1008ms
rtt min/avg/max/mdev = 3.681/5.867/8.054/2.186 ms

root@centralRouter:~# ip route get 192.168.255.13
192.168.255.13 dev eth1 src 192.168.255.14 uid 0 
    cache 




root@inetRouter2:~# ping -c 4 192.168.255.14
PING 192.168.255.14 (192.168.255.14) 56(84) bytes of data.
64 bytes from 192.168.255.14: icmp_seq=1 ttl=64 time=5.65 ms
64 bytes from 192.168.255.14: icmp_seq=2 ttl=64 time=4.14 ms
^C
--- 192.168.255.14 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 4.140/4.896/5.652/0.756 ms

root@inetRouter2:~# ip route get 192.168.255.14
192.168.255.14 dev eth1 src 192.168.255.13 uid 0 
    cache 



root@centralRouter:~# nano /etc/netplan/50-vagrant.yaml

root@centralRouter:~# cat /etc/netplan/50-vagrant.yaml
---
network:
  version: 2
  renderer: networkd
  ethernets:
    eth1:   # 192.168.255.2/30 → inetRouter
      addresses:
        - 192.168.255.2/30
        - 192.168.255.14/30
      routes:
        - to: 0.0.0.0/0
          via: 192.168.255.1
    eth2:   # 192.168.0.1/28 directors
      addresses:
        - 192.168.0.1/28
    eth3:   # 192.168.0.33/28 office hardware
      addresses:
        - 192.168.0.33/28
    eth4:   # 192.168.0.65/26 wifi
      addresses:
        - 192.168.0.65/26
    eth5:   # 192.168.255.9/30 → office1Router
      addresses:
        - 192.168.255.9/30
      routes:
        - to: 192.168.2.0/24
          via: 192.168.255.10
    eth6:   # 192.168.255.5/30 → office2Router
      addresses:
        - 192.168.255.5/30
      routes:
        - to: 192.168.1.0/24
          via: 192.168.255.6
    eth7:   # 192.168.50.11
      addresses:
        - 192.168.50.11/24


root@centralRouter:~# netplan apply

root@centralRouter:~# ping -c 3 192.168.255.13
PING 192.168.255.13 (192.168.255.13) 56(84) bytes of data.
64 bytes from 192.168.255.13: icmp_seq=1 ttl=64 time=5.92 ms
64 bytes from 192.168.255.13: icmp_seq=2 ttl=64 time=5.26 ms
^C
--- 192.168.255.13 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1012ms
rtt min/avg/max/mdev = 5.259/5.589/5.920/0.330 ms





root@inetRouter2:~# cat > /etc/sysctl.d/99-ip-forward.conf <<'EOF'
net.ipv4.ip_forward = 1
EOF

root@inetRouter2:~# sysctl --system
* Applying /usr/lib/sysctl.d/10-apparmor.conf ...
* Applying /usr/lib/sysctl.d/10-coredump-debian.conf ...
* Applying /usr/lib/sysctl.d/50-default.conf ...
* Applying /usr/lib/sysctl.d/50-pid-max.conf ...
* Applying /usr/lib/sysctl.d/55-bufferbloat.conf ...
* Applying /usr/lib/sysctl.d/55-console-messages.conf ...
* Applying /usr/lib/sysctl.d/55-ipv6-privacy.conf ...
* Applying /usr/lib/sysctl.d/55-kernel-hardening.conf ...
* Applying /usr/lib/sysctl.d/55-magic-sysrq.conf ...
* Applying /usr/lib/sysctl.d/55-map-count.conf ...
* Applying /usr/lib/sysctl.d/55-network-security.conf ...
* Applying /usr/lib/sysctl.d/55-ptrace.conf ...
* Applying /usr/lib/sysctl.d/55-zeropage.conf ...
* Applying /etc/sysctl.d/99-ip-forward.conf ...
kernel.apparmor_restrict_unprivileged_userns = 1
kernel.apparmor_restrict_unprivileged_unconfined = 1
kernel.core_pattern = core
kernel.sysrq = 0x01b6
kernel.core_uses_pid = 1
net.ipv4.conf.default.rp_filter = 2
net.ipv4.conf.enp0s3.rp_filter = 2
net.ipv4.conf.eth1.rp_filter = 2
net.ipv4.conf.eth2.rp_filter = 2
net.ipv4.conf.lo.rp_filter = 2
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.enp0s3.accept_source_route = 0
net.ipv4.conf.eth1.accept_source_route = 0
net.ipv4.conf.eth2.accept_source_route = 0
net.ipv4.conf.lo.accept_source_route = 0
net.ipv4.conf.default.promote_secondaries = 1
net.ipv4.conf.enp0s3.promote_secondaries = 1
net.ipv4.conf.eth1.promote_secondaries = 1
net.ipv4.conf.eth2.promote_secondaries = 1
net.ipv4.conf.lo.promote_secondaries = 1
net.ipv4.ping_group_range = 0 2147483647
net.core.default_qdisc = fq_codel
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
fs.protected_regular = 2
fs.protected_fifos = 1
vm.max_map_count = 1048576
kernel.pid_max = 4194304
net.core.default_qdisc = fq_codel
kernel.printk = 4 4 1 7
net.ipv6.conf.all.use_tempaddr = 2
net.ipv6.conf.default.use_tempaddr = 2
kernel.kptr_restrict = 1
kernel.sysrq = 176
vm.max_map_count = 1048576
net.ipv4.conf.default.rp_filter = 2
net.ipv4.conf.all.rp_filter = 2
kernel.yama.ptrace_scope = 1
vm.mmap_min_addr = 65536
net.ipv4.ip_forward = 1


root@inetRouter2:~# sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 1

root@inetRouter2:~# ip route replace 192.168.0.0/24 via 192.168.255.14

root@inetRouter2:~# ip route get 192.168.0.2
192.168.0.2 via 192.168.255.14 dev eth1 src 192.168.255.13 uid 0 
    cache 

root@inetRouter2:~# ping -c 3 192.168.0.2
PING 192.168.0.2 (192.168.0.2) 56(84) bytes of data.
64 bytes from 192.168.0.2: icmp_seq=1 ttl=63 time=10.4 ms
64 bytes from 192.168.0.2: icmp_seq=2 ttl=63 time=9.86 ms
^C
--- 192.168.0.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1028ms
rtt min/avg/max/mdev = 9.859/10.139/10.420/0.280 ms


root@inetRouter2:~# ip route replace default via 192.168.255.14

root@inetRouter2:~# ip route
default via 192.168.255.14 dev eth1 
default via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
2.144.48.252 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100 
10.0.2.2 dev enp0s3 proto dhcp scope link src 10.0.2.15 metric 100 
45.144.48.252 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
45.144.48.253 via 10.0.2.2 dev enp0s3 proto dhcp src 10.0.2.15 metric 100 
192.168.0.0/24 via 192.168.255.14 dev eth1 
192.168.56.0/24 dev eth2 proto kernel scope link src 192.168.56.20 
192.168.255.12/30 dev eth1 proto kernel scope link src 192.168.255.13 


root@inetRouter2:~# ip route get 8.8.8.8
8.8.8.8 via 192.168.255.14 dev eth1 src 192.168.255.13 uid 0 
    cache 

root@centralServer:~# apt-get install -y nginx

root@centralServer:~# systemctl enable --now nginx
Synchronizing state of nginx.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable nginx

root@centralServer:~# systemctl status nginx --no-pager
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-07-31 14:22:06 UTC; 1min 9s ago
 Invocation: dd54c05a53a444b5adada88b6f5a6b39
       Docs: man:nginx(8)
   Main PID: 8985 (nginx)
      Tasks: 2 (limit: 739)
     Memory: 2.3M (peak: 5.1M)
        CPU: 246ms
     CGroup: /system.slice/nginx.service
             ├─8985 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
             └─8988 "nginx: worker process"

Jul 31 14:22:05 centralServer systemd[1]: Starting nginx.service - A high performance web server …ver...
Jul 31 14:22:06 centralServer systemd[1]: Started nginx.service - A high performance web server a…erver.
Hint: Some lines were ellipsized, use -l to show in full.



root@centralServer:~# cat > /var/www/html/index.html <<'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iptables homework</title>
</head>
<body>
    <h1>centralServer nginx works</h1>
    <p>The request was forwarded through inetRouter2.</p>
</body>
</html>
EOF


root@centralServer:~# curl http://127.0.0.1/
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iptables homework</title>
</head>
<body>
    <h1>centralServer nginx works</h1>
    <p>The request was forwarded through inetRouter2.</p>
</body>
</html>

root@centralServer:~# curl http://192.168.0.2/
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iptables homework</title>
</head>
<body>
    <h1>centralServer nginx works</h1>
    <p>The request was forwarded through inetRouter2.</p>
</body>
</html>



root@inetRouter2:~# curl -v http://192.168.0.2/
*   Trying 192.168.0.2:80...
* Established connection to 192.168.0.2 (192.168.0.2 port 80) from 192.168.255.13 port 35568 
* using HTTP/1.x
> GET / HTTP/1.1
> Host: 192.168.0.2
> User-Agent: curl/8.18.0
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 200 OK
< Server: nginx/1.28.3 (Ubuntu)
< Date: Fri, 31 Jul 2026 14:26:19 GMT
< Content-Type: text/html
< Content-Length: 232
< Last-Modified: Fri, 31 Jul 2026 14:24:41 GMT
< Connection: keep-alive
< ETag: "6a6cb029-e8"
< Accept-Ranges: bytes
< 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iptables homework</title>
</head>
<body>
    <h1>centralServer nginx works</h1>
    <p>The request was forwarded through inetRouter2.</p>
</body>
</html>
* Connection #0 to host 192.168.0.2:80 left intact

root@inetRouter2:~# ip -o -4 address show
1: lo    inet 127.0.0.1/8 scope host lo\       valid_lft forever preferred_lft forever
2: enp0s3    inet 10.0.2.15/24 metric 100 brd 10.0.2.255 scope global dynamic enp0s3\       valid_lft 84331sec preferred_lft 84331sec
3: eth1    inet 192.168.255.13/30 brd 192.168.255.15 scope global eth1\       valid_lft forever preferred_lft forever
4: eth2    inet 192.168.56.20/24 brd 192.168.56.255 scope global eth2\       valid_lft forever preferred_lft forever
root@inetRouter2:~# INTERNAL_IF=$(ip -o -4 address show \
  | awk '$4 ~ /^192\.168\.255\.13\// {print $2}')
root@inetRouter2:~# HOSTONLY_IF=$(ip -o -4 address show \
  | awk '$4 ~ /^192\.168\.56\.20\// {print $2}')
root@inetRouter2:~# echo "INTERNAL_IF=${INTERNAL_IF}"
echo "HOSTONLY_IF=${HOSTONLY_IF}"
INTERNAL_IF=eth1
HOSTONLY_IF=eth2

root@inetRouter2:~# test -n "${INTERNAL_IF}" && echo "Internal interface found"
test -n "${HOSTONLY_IF}" && echo "Host-only interface found"
Internal interface found
Host-only interface found

root@inetRouter2:~# iptables -t nat -A PREROUTING \
  -i "${HOSTONLY_IF}" \
  -p tcp \
  -d 192.168.56.20 \
  --dport 8080 \
  -j DNAT \
  --to-destination 192.168.0.2:80

root@inetRouter2:~# iptables -t nat -L PREROUTING -n -v --line-numbers
Chain PREROUTING (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 DNAT       tcp  --  eth2   *       0.0.0.0/0            192.168.56.20        tcp dpt:8080 to:192.168.0.2:80




root@inetRouter2:~# iptables -A FORWARD \
  -i "${HOSTONLY_IF}" \
  -o "${INTERNAL_IF}" \
  -p tcp \
  -s 192.168.56.0/24 \
  -d 192.168.0.2 \
  --dport 80 \
  -m conntrack --ctstate NEW \
  -j ACCEPT


root@inetRouter2:~# iptables -A FORWARD \
  -m conntrack \
  --ctstate ESTABLISHED,RELATED \
  -j ACCEPT


root@inetRouter2:~# iptables -L FORWARD -n -v --line-numbers
Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        0     0 ACCEPT     tcp  --  eth2   eth1    192.168.56.0/24      192.168.0.2          tcp dpt:80 ctstate NEW
2        0     0 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED



[root@otus-homework otus-linux]# vagrant ssh centralRouter

vagrant@centralRouter:~$ sudo -i

root@centralRouter:~# ip route replace 192.168.56.0/24 via 192.168.255.13

root@centralRouter:~# ip route get 192.168.56.1
192.168.56.1 via 192.168.255.13 dev eth1 src 192.168.255.14 uid 0 
    cache 




[root@otus-homework otus-linux]# curl -v http://192.168.56.20:8080/
*   Trying 192.168.56.20...
* TCP_NODELAY set
* Connected to 192.168.56.20 (192.168.56.20) port 8080 (#0)
> GET / HTTP/1.1
> Host: 192.168.56.20:8080
> User-Agent: curl/7.61.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.28.3 (Ubuntu)
< Date: Fri, 31 Jul 2026 14:40:26 GMT
< Content-Type: text/html
< Content-Length: 232
< Last-Modified: Fri, 31 Jul 2026 14:24:41 GMT
< Connection: keep-alive
< ETag: "6a6cb029-e8"
< Accept-Ranges: bytes
< 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iptables homework</title>
</head>
<body>
    <h1>centralServer nginx works</h1>
    <p>The request was forwarded through inetRouter2.</p>
</body>
</html>
* Connection #0 to host 192.168.56.20 left intact
[root@otus-homework otus-linux]# 


root@inetRouter2:~# iptables -t nat -L PREROUTING -n -v --line-numbers
Chain PREROUTING (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        1    60 DNAT       tcp  --  eth2   *       0.0.0.0/0            192.168.56.20        tcp dpt:8080 to:192.168.0.2:80
root@inetRouter2:~# iptables -L FORWARD -n -v --line-numbers
Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        1    60 ACCEPT     tcp  --  eth2   eth1    192.168.56.0/24      192.168.0.2          tcp dpt:80 ctstate NEW
2        9  1036 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED

root@inetRouter2:~# iptables -t nat -L PREROUTING -n -v --line-numbers
Chain PREROUTING (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        3   180 DNAT       tcp  --  eth2   *       0.0.0.0/0            192.168.56.20        tcp dpt:8080 to:192.168.0.2:80

root@inetRouter2:~# iptables -L FORWARD -n -v --line-numbers
Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1        3   180 ACCEPT     tcp  --  eth2   eth1    192.168.56.0/24      192.168.0.2          tcp dpt:80 ctstate NEW
2       27  3108 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED


root@centralServer:~# tail -n 10 /var/log/nginx/access.log
127.0.0.1 - - [31/Jul/2026:14:25:06 +0000] "GET / HTTP/1.1" 200 232 "-" "curl/8.18.0"
192.168.0.2 - - [31/Jul/2026:14:25:38 +0000] "GET / HTTP/1.1" 200 232 "-" "curl/8.18.0"
192.168.255.13 - - [31/Jul/2026:14:26:19 +0000] "GET / HTTP/1.1" 200 232 "-" "curl/8.18.0"
192.168.56.1 - - [31/Jul/2026:14:40:26 +0000] "GET / HTTP/1.1" 200 232 "-" "curl/7.61.1"
192.168.56.1 - - [31/Jul/2026:14:41:23 +0000] "GET / HTTP/1.1" 200 232 "-" "curl/7.61.1"
192.168.56.1 - - [31/Jul/2026:14:41:24 +0000] "GET / HTTP/1.1" 200 232 "-" "curl/7.61.1"

```
