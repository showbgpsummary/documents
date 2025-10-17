# Problem(s)
First of all,pinging to 172.20.20.x is acting unstable.One deploy you can ping sonic4,the other you can ping but can't ping the other(example).
I tested it multiple times and the numbers are totally random.I tested it many times with other sleep configs. 
The other part is,sshd is failed if you don't restart it.Basicly entering service ssh restart is solving the problem.
## Summary
SSH problem can be solved easily.ip link set up problem cannot be solved without sleep,with sleep 3 and 5.sleep 10 is enough to solve the problem,I think it's a race condition.Working on the root couse.
logs:
## SSH TEST 
➜  ~ containerlab deploy 
19:59:04 INFO Containerlab started version=0.71.0
19:59:04 INFO Parsing & checking topology file=lab2-sonic.clab.yml
19:59:04 INFO Creating docker network name=clab IPv4 subnet=172.20.20.0/24 IPv6 subnet=3fff:172:20:20::/64 MTU=0
19:59:04 INFO Creating lab directory path=/home/sysadmin/clab-testgcp
19:59:04 INFO Creating container name=sonic3
19:59:04 INFO Creating container name=sonic4
19:59:04 INFO Creating container name=sonic2
19:59:04 INFO Creating container name=sonic
19:59:04 INFO Created link: sonic:eth1 ▪┄┄▪ sonic2:eth1
19:59:04 INFO Executed command node=sonic3 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

19:59:04 INFO Executed command node=sonic3 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

19:59:04 INFO Executed command node=sonic command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

19:59:04 INFO Executed command node=sonic4 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

19:59:04 INFO Executed command node=sonic4 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

19:59:04 INFO Executed command node=sonic2 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

19:59:04 INFO Executed command node=sonic2 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

19:59:04 INFO Adding host entries path=/etc/hosts
19:59:04 INFO Adding SSH config for nodes path=/etc/ssh/ssh_config.d/clab-testgcp.conf
You are on the latest version (0.71.0)
╭─────────────────────┬─────────────────┬─────────┬───────────────────╮
│         Name        │    Kind/Image   │  State  │   IPv4/6 Address  │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic  │ sonic-vs        │ running │ 172.20.20.2       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::2 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic2 │ sonic-vs        │ running │ 172.20.20.4       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::4 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic3 │ sonic-vs        │ running │ 172.20.20.3       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::3 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic4 │ sonic-vs        │ running │ 172.20.20.5       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::5 │
╰─────────────────────┴─────────────────┴─────────┴───────────────────╯
➜  ~ ping 172.20.20.2 
PING 172.20.20.2 (172.20.20.2) 56(84) bytes of data.
64 bytes from 172.20.20.2: icmp_seq=1 ttl=64 time=0.101 ms
^C
--- 172.20.20.2 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.101/0.101/0.101/0.000 ms
➜  ~ ping 172.20.20.3 
PING 172.20.20.3 (172.20.20.3) 56(84) bytes of data.
From 172.20.20.1 icmp_seq=1 Destination Host Unreachable
From 172.20.20.1 icmp_seq=2 Destination Host Unreachable
From 172.20.20.1 icmp_seq=3 Destination Host Unreachable
^C
--- 172.20.20.3 ping statistics ---
4 packets transmitted, 0 received, +3 errors, 100% packet loss, time 3068ms
pipe 3
➜  ~ ping 172.20.20.4
PING 172.20.20.4 (172.20.20.4) 56(84) bytes of data.
64 bytes from 172.20.20.4: icmp_seq=1 ttl=64 time=0.135 ms
64 bytes from 172.20.20.4: icmp_seq=2 ttl=64 time=0.047 ms
^C
--- 172.20.20.4 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1017ms
rtt min/avg/max/mdev = 0.047/0.091/0.135/0.044 ms
➜  ~ ping 172.20.20.5
PING 172.20.20.5 (172.20.20.5) 56(84) bytes of data.
From 172.20.20.1 icmp_seq=1 Destination Host Unreachable
From 172.20.20.1 icmp_seq=2 Destination Host Unreachable
From 172.20.20.1 icmp_seq=3 Destination Host Unreachable
^C
--- 172.20.20.5 ping statistics ---
4 packets transmitted, 0 received, +3 errors, 100% packet loss, time 3085ms
pipe 4
➜  ~ docker exec -it clab-testgcp-sonic service ssh status 
sshd is not running ... failed!
➜  ~ docker exec -it clab-testgcp-sonic2 service ssh status 
sshd is running.
➜  ~ 
## LOGS WITH IP LINK SET UP 
➜  ~ containerlab deploy 
20:02:17 INFO Containerlab started version=0.71.0
20:02:17 INFO Parsing & checking topology file=lab2-sonic.clab.yml
20:02:17 INFO Creating docker network name=clab IPv4 subnet=172.20.20.0/24 IPv6 subnet=3fff:172:20:20::/64 MTU=0
20:02:17 INFO Creating lab directory path=/home/sysadmin/clab-testgcp
20:02:17 INFO Creating container name=sonic2
20:02:17 INFO Creating container name=sonic4
20:02:17 INFO Creating container name=sonic
20:02:17 INFO Creating container name=sonic3
20:02:18 INFO Created link: sonic:eth1 ▪┄┄▪ sonic2:eth1
20:02:18 INFO Executed command node=sonic2 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:02:18 INFO Executed command node=sonic2 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:02:18 INFO Executed command node=sonic2 command="ip link set eth0 up" stdout=""
20:02:18 INFO Executed command node=sonic4 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:02:18 INFO Executed command node=sonic4 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:02:18 INFO Executed command node=sonic4 command="ip link set eth0 up" stdout=""
20:02:18 INFO Executed command node=sonic command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:02:18 INFO Executed command node=sonic command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:02:18 INFO Executed command node=sonic command="ip link set eth0 up" stdout=""
20:02:18 INFO Executed command node=sonic3 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:02:18 INFO Executed command node=sonic3 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:02:18 INFO Executed command node=sonic3 command="ip link set eth0 up" stdout=""
20:02:18 INFO Adding host entries path=/etc/hosts
20:02:18 INFO Adding SSH config for nodes path=/etc/ssh/ssh_config.d/clab-testgcp.conf
You are on the latest version (0.71.0)
╭─────────────────────┬─────────────────┬─────────┬───────────────────╮
│         Name        │    Kind/Image   │  State  │   IPv4/6 Address  │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic  │ sonic-vs        │ running │ 172.20.20.2       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::2 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic2 │ sonic-vs        │ running │ 172.20.20.4       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::4 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic3 │ sonic-vs        │ running │ 172.20.20.5       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::5 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic4 │ sonic-vs        │ running │ 172.20.20.3       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::3 │
╰─────────────────────┴─────────────────┴─────────┴───────────────────╯
➜  ~ ping 172.20.20.2
PING 172.20.20.2 (172.20.20.2) 56(84) bytes of data.
64 bytes from 172.20.20.2: icmp_seq=1 ttl=64 time=0.097 ms
--- 172.20.20.2 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.097/0.097/0.097/0.000 ms
➜  ~ ping 172.20.20.3
PING 172.20.20.3 (172.20.20.3) 56(84) bytes of data.
64 bytes from 172.20.20.3: icmp_seq=1 ttl=64 time=0.092 ms
--- 172.20.20.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.092/0.092/0.092/0.000 ms
➜  ~ ping 172.20.20.4
PING 172.20.20.4 (172.20.20.4) 56(84) bytes of data.
64 bytes from 172.20.20.4: icmp_seq=1 ttl=64 time=0.070 ms
--- 172.20.20.4 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.070/0.070/0.070/0.000 ms
➜  ~ ping 172.20.20.5
PING 172.20.20.5 (172.20.20.5) 56(84) bytes of data.
From 172.20.20.1 icmp_seq=1 Destination Host Unreachable
From 172.20.20.1 icmp_seq=2 Destination Host Unreachable
From 172.20.20.1 icmp_seq=3 Destination Host Unreachable
--- 172.20.20.5 ping statistics ---
4 packets transmitted, 0 received, +3 errors, 100% packet loss, time 3064ms
pipe 4
➜  ~ docker exec -it clab-testgcp-sonic3 ip addr     
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0@if141: <BROADCAST,MULTICAST,PROMISC> mtu 9122 qdisc noqueue state DOWN group default 
    link/ether 66:15:46:6f:be:14 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.20.20.5/24 brd 172.20.20.255 scope global eth0
       valid_lft forever preferred_lft forever
➜  ~ docker exec -it clab-testgcp-sonic3 ip link set eth0 up
➜  ~ docker exec -it clab-testgcp-sonic3 ip addr            
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0@if141: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 9122 qdisc noqueue state UP group default 
    link/ether 66:15:46:6f:be:14 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.20.20.5/24 brd 172.20.20.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::6415:46ff:fe6f:be14/64 scope link 
       valid_lft forever preferred_lft forever
#### (deleted other interface configs because it makes the log harder to find what you want) 
## LOGS WITH SLEEP 3 AND IP LINK SET UP 
➜  ~ containerlab deploy 
20:12:47 INFO Containerlab started version=0.71.0
20:12:47 INFO Parsing & checking topology file=lab2-sonic.clab.yml
20:12:47 INFO Creating docker network name=clab IPv4 subnet=172.20.20.0/24 IPv6 subnet=3fff:172:20:20::/64 MTU=0
20:12:47 INFO Creating lab directory path=/home/sysadmin/clab-testgcp
20:12:47 INFO Creating container name=sonic3
20:12:47 INFO Creating container name=sonic
20:12:47 INFO Creating container name=sonic4
20:12:47 INFO Creating container name=sonic2
20:12:47 INFO Created link: sonic:eth1 ▪┄┄▪ sonic2:eth1
20:12:50 INFO Executed command node=sonic3 command="sleep 3" stdout=""
20:12:50 INFO Executed command node=sonic3 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:12:50 INFO Executed command node=sonic3 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:12:50 INFO Executed command node=sonic3 command="ip link set eth0 up" stdout=""
20:12:50 INFO Executed command node=sonic4 command="sleep 3" stdout=""
20:12:50 INFO Executed command node=sonic4 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:12:50 INFO Executed command node=sonic4 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:12:50 INFO Executed command node=sonic4 command="ip link set eth0 up" stdout=""
20:12:50 INFO Executed command node=sonic2 command="sleep 3" stdout=""
20:12:50 INFO Executed command node=sonic2 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:12:50 INFO Executed command node=sonic2 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:12:50 INFO Executed command node=sonic2 command="ip link set eth0 up" stdout=""
20:12:50 INFO Executed command node=sonic command="sleep 3" stdout=""
20:12:50 INFO Executed command node=sonic command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:12:50 INFO Executed command node=sonic command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:12:50 INFO Executed command node=sonic command="ip link set eth0 up" stdout=""
20:12:50 INFO Adding host entries path=/etc/hosts
20:12:50 INFO Adding SSH config for nodes path=/etc/ssh/ssh_config.d/clab-testgcp.conf
You are on the latest version (0.71.0)
╭─────────────────────┬─────────────────┬─────────┬───────────────────╮
│         Name        │    Kind/Image   │  State  │   IPv4/6 Address  │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic  │ sonic-vs        │ running │ 172.20.20.5       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::5 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic2 │ sonic-vs        │ running │ 172.20.20.4       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::4 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic3 │ sonic-vs        │ running │ 172.20.20.2       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::2 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic4 │ sonic-vs        │ running │ 172.20.20.3       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::3 │
╰─────────────────────┴─────────────────┴─────────┴───────────────────╯
➜  ~ ping 172.20.20.2
PING 172.20.20.2 (172.20.20.2) 56(84) bytes of data.
^C
--- 172.20.20.2 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2088ms

➜  ~ ping 172.20.20.3
PING 172.20.20.3 (172.20.20.3) 56(84) bytes of data.
^C
--- 172.20.20.3 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2067ms

➜  ~ ping 172.20.20.4
PING 172.20.20.4 (172.20.20.4) 56(84) bytes of data.
64 bytes from 172.20.20.4: icmp_seq=1 ttl=64 time=0.118 ms
64 bytes from 172.20.20.4: icmp_seq=2 ttl=64 time=0.070 ms
64 bytes from 172.20.20.4: icmp_seq=3 ttl=64 time=0.070 ms
^C
--- 172.20.20.4 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2087ms
rtt min/avg/max/mdev = 0.070/0.086/0.118/0.022 ms
➜  ~ ping 172.20.20.5
PING 172.20.20.5 (172.20.20.5) 56(84) bytes of data.
64 bytes from 172.20.20.5: icmp_seq=1 ttl=64 time=0.139 ms
64 bytes from 172.20.20.5: icmp_seq=2 ttl=64 time=0.058 ms
^C
--- 172.20.20.5 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1042ms
rtt min/avg/max/mdev = 0.058/0.098/0.139/0.040 ms
➜  ~ docker exec -it clab-testgcp-sonic3 ip addr 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0@if149: <BROADCAST,MULTICAST,PROMISC> mtu 9122 qdisc noqueue state DOWN group default 
    link/ether de:e6:a2:2f:9f:34 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.20.20.2/24 brd 172.20.20.255 scope global eth0
       valid_lft forever preferred_lft forever
## LOG WITH SLEEP 10+ IP LINK SET UP 
➜  ~ containerlab deploy 
20:16:23 INFO Containerlab started version=0.71.0
20:16:23 INFO Parsing & checking topology file=lab2-sonic.clab.yml
20:16:23 INFO Creating docker network name=clab IPv4 subnet=172.20.20.0/24 IPv6 subnet=3fff:172:20:20::/64 MTU=0
20:16:23 INFO Creating lab directory path=/home/sysadmin/clab-testgcp
20:16:23 INFO Creating container name=sonic4
20:16:23 INFO Creating container name=sonic2
20:16:23 INFO Creating container name=sonic
20:16:23 INFO Creating container name=sonic3
20:16:23 INFO Created link: sonic:eth1 ▪┄┄▪ sonic2:eth1
20:16:33 INFO Executed command node=sonic2 command="sleep 10" stdout=""
20:16:33 INFO Executed command node=sonic2 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:16:33 INFO Executed command node=sonic2 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:16:33 INFO Executed command node=sonic2 command="ip link set eth0 up" stdout=""
20:16:33 INFO Executed command node=sonic4 command="sleep 10" stdout=""
20:16:33 INFO Executed command node=sonic4 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:16:33 INFO Executed command node=sonic4 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:16:33 INFO Executed command node=sonic4 command="ip link set eth0 up" stdout=""
20:16:33 INFO Executed command node=sonic command="sleep 10" stdout=""
20:16:33 INFO Executed command node=sonic command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:16:33 INFO Executed command node=sonic command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:16:33 INFO Executed command node=sonic command="ip link set eth0 up" stdout=""
20:16:33 INFO Executed command node=sonic3 command="sleep 10" stdout=""
20:16:33 INFO Executed command node=sonic3 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:16:33 INFO Executed command node=sonic3 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:16:33 INFO Executed command node=sonic3 command="ip link set eth0 up" stdout=""
20:16:33 INFO Adding host entries path=/etc/hosts
20:16:33 INFO Adding SSH config for nodes path=/etc/ssh/ssh_config.d/clab-testgcp.conf
You are on the latest version (0.71.0)
╭─────────────────────┬─────────────────┬─────────┬───────────────────╮
│         Name        │    Kind/Image   │  State  │   IPv4/6 Address  │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic  │ sonic-vs        │ running │ 172.20.20.2       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::2 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic2 │ sonic-vs        │ running │ 172.20.20.4       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::4 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic3 │ sonic-vs        │ running │ 172.20.20.3       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::3 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic4 │ sonic-vs        │ running │ 172.20.20.5       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::5 │
╰─────────────────────┴─────────────────┴─────────┴───────────────────╯
➜  ~ ping 172.20.20.2 
PING 172.20.20.2 (172.20.20.2) 56(84) bytes of data.
64 bytes from 172.20.20.2: icmp_seq=1 ttl=64 time=0.113 ms
^C
--- 172.20.20.2 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.113/0.113/0.113/0.000 ms
➜  ~ ping 172.20.20.3 
PING 172.20.20.3 (172.20.20.3) 56(84) bytes of data.
64 bytes from 172.20.20.3: icmp_seq=1 ttl=64 time=0.193 ms
^C
--- 172.20.20.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.193/0.193/0.193/0.000 ms
➜  ~ ping 172.20.20.4
PING 172.20.20.4 (172.20.20.4) 56(84) bytes of data.
64 bytes from 172.20.20.4: icmp_seq=1 ttl=64 time=0.116 ms
^C
--- 172.20.20.4 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.116/0.116/0.116/0.000 ms
➜  ~ ping 172.20.20.5
PING 172.20.20.5 (172.20.20.5) 56(84) bytes of data.
64 bytes from 172.20.20.5: icmp_seq=1 ttl=64 time=0.130 ms
64 bytes from 172.20.20.5: icmp_seq=2 ttl=64 time=0.047 ms
^C
--- 172.20.20.5 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1047ms
rtt min/avg/max/mdev = 0.047/0.088/0.130/0.041 ms
➜  ~ 
## LOG WITH SLEEP 5 + IP LINK SET UP 
➜  ~ containerlab deploy 
20:19:41 INFO Containerlab started version=0.71.0
20:19:41 INFO Parsing & checking topology file=lab2-sonic.clab.yml
20:19:41 INFO Creating docker network name=clab IPv4 subnet=172.20.20.0/24 IPv6 subnet=3fff:172:20:20::/64 MTU=0
20:19:41 INFO Creating lab directory path=/home/sysadmin/clab-testgcp
20:19:41 INFO Creating container name=sonic
20:19:41 INFO Creating container name=sonic3
20:19:41 INFO Creating container name=sonic2
20:19:41 INFO Creating container name=sonic4
20:19:41 INFO Created link: sonic:eth1 ▪┄┄▪ sonic2:eth1
20:19:46 INFO Executed command node=sonic2 command="sleep 5" stdout=""
20:19:46 INFO Executed command node=sonic2 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:19:46 INFO Executed command node=sonic2 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:19:46 INFO Executed command node=sonic2 command="ip link set eth0 up" stdout=""
20:19:46 INFO Executed command node=sonic3 command="sleep 5" stdout=""
20:19:46 INFO Executed command node=sonic3 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:19:46 INFO Executed command node=sonic3 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:19:46 INFO Executed command node=sonic3 command="ip link set eth0 up" stdout=""
20:19:46 INFO Executed command node=sonic4 command="sleep 5" stdout=""
20:19:46 INFO Executed command node=sonic4 command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:19:46 INFO Executed command node=sonic4 command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:19:46 INFO Executed command node=sonic4 command="ip link set eth0 up" stdout=""
20:19:46 INFO Executed command node=sonic command="sleep 5" stdout=""
20:19:46 INFO Executed command node=sonic command="echo admin:admin123 | sudo chpasswd"
  stdout=
  │ admin:admin123 | sudo chpasswd

20:19:46 INFO Executed command node=sonic command="service ssh restart"
  stdout=
  │ Restarting OpenBSD Secure Shell server: sshd.

20:19:46 INFO Executed command node=sonic command="ip link set eth0 up" stdout=""
20:19:46 INFO Adding host entries path=/etc/hosts
20:19:46 INFO Adding SSH config for nodes path=/etc/ssh/ssh_config.d/clab-testgcp.conf
You are on the latest version (0.71.0)
╭─────────────────────┬─────────────────┬─────────┬───────────────────╮
│         Name        │    Kind/Image   │  State  │   IPv4/6 Address  │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic  │ sonic-vs        │ running │ 172.20.20.2       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::2 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic2 │ sonic-vs        │ running │ 172.20.20.3       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::3 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic3 │ sonic-vs        │ running │ 172.20.20.5       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::5 │
├─────────────────────┼─────────────────┼─────────┼───────────────────┤
│ clab-testgcp-sonic4 │ sonic-vs        │ running │ 172.20.20.4       │
│                     │ docker-sonic-vs │         │ 3fff:172:20:20::4 │
╰─────────────────────┴─────────────────┴─────────┴───────────────────╯
➜  ~ ping 172.20.20.2
PING 172.20.20.2 (172.20.20.2) 56(84) bytes of data.
64 bytes from 172.20.20.2: icmp_seq=1 ttl=64 time=0.125 ms
^C
--- 172.20.20.2 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.125/0.125/0.125/0.000 ms
➜  ~ ping 172.20.20.3
PING 172.20.20.3 (172.20.20.3) 56(84) bytes of data.
64 bytes from 172.20.20.3: icmp_seq=1 ttl=64 time=0.152 ms
^C
--- 172.20.20.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.152/0.152/0.152/0.000 ms
➜  ~ ping 172.20.20.4
PING 172.20.20.4 (172.20.20.4) 56(84) bytes of data.
^C
--- 172.20.20.4 ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1015ms

➜  ~ ping 172.20.20.5
PING 172.20.20.5 (172.20.20.5) 56(84) bytes of data.
^C
--- 172.20.20.5 ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1060ms

➜  ~ 
 # `docker-sonic-vs` Image: Recurring Control Plane (Redis/SWSS) Startup Failure (`Connection refused`) and Confirmed Race Condition 

### Environment Details:
- Containerlab Version: 0.71.0 
- Network OS (NOS) Image:`docker-sonic-vs` latest in 10/15/2025.
**Symptoms:**

1.  **Recurring Control Plane Crash (Redis):** CLI commands like `show interface status` fail because they cannot connect to the main state database (Redis).

    Error Output:
    ```
    root@sonic:/# show interface status Ethernet0-100
    RuntimeError: Unable to connect to redis - Connection refused(1): Cannot assign requested address
(some deployements it show the interfaces,sometimes don't.)
    ```
2. **SSH is failed on boot,starts on a simple service ssh restart**
Whenever you deploy the container and check service status of ssh,it's failed.It needs a basic restart to run.It's out of scope for me.


---
**Update: Root Cause Analysis (Confirmed Race Condition)**

Detailed syslog analysis confirms that the issue is a definitive **race condition** rooted in the microservice startup timing:

**Analysis Details:**
Syslogs show that the issue originates within a critical 1-second window during service initialization:

1.  The `portsyncd` service starts and immediately attempts to read the state of physical ports (e.g., Ethernet0).
2.  This reading attempt occurs too early or too quickly while critical Control Plane services (Redis/SWSS) are not fully initialized and listening.
3.  The result is a failure to publish/retrieve port status (`ok:down`) which cascades into the `orchagent` and leads to a full control plane halt (`Connection refused`).
4.  The success or failure of the boot process is may dependent on microscopic fluctuations in CPU load and service placement speed within the shared Docker environment, confirming a timing-based race condition.As I see in numberless logs and deploys,sometimes it's working fine with pure luck,and sometimes isn't.

**Fix**
executing ip or using link set eth0 up after sleep of 10 seconds(via exec:) just covers it up for the lab.5 isn't enough.
I'm not sure this things are the only strange behavior in docker-sonic-vs.Maybe I'll keep researching.
same issues from 2021(seen after troubleshooting logs): https://groups.google.com/g/sonicproject/c/-vIipAPlHLA


[logs.txt](https://github.com/user-attachments/files/22936216/logs.txt)
