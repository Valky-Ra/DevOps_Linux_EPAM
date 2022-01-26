## 1. add secondary ip address to you second network interface enp0s8. Each point must be presented with commands and showing that new address was applied to the interface. To repeat adding address for points 2 and 3 address must be deleted (please add deleting address to you homework log) 
Ну, для начала нужно проверить какие у меня соединения изначально есть.
Сделаем это командой ip -4 a
```
[ValkyRa@centos ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.0.106/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s3
       valid_lft 6684sec preferred_lft 6684sec
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```
Methods:
### 1. using ip utility (stateless)
 
Поскольку у меня не используется интерфейс enp0s8, посему процедуры будем проводить с enp0s3. Используем утилиту ip, для добавления вторичного IP в интерфейс.
```
[ValkyRa@centos ~]$ sudo ip addr add 192.168.0.124/24 dev enp0s3
[sudo] пароль для ValkyRa: 
```

После успешного добавления, проверим снова командой IP, появился ли новый IP в интерфейсе 
```
[ValkyRa@centos ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.0.106/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s3
       valid_lft 5904sec preferred_lft 5904sec
    inet 192.168.0.124/24 scope global secondary enp0s3
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```       
Для полноты картины, что IP работает корректно, можно попробовать пингануть его.
```
[ValkyRa@centos ~]$ ping 192.168.0.124
PING 192.168.0.124 (192.168.0.124) 56(84) bytes of data.
64 bytes from 192.168.0.124: icmp_seq=1 ttl=64 time=0.060 ms
64 bytes from 192.168.0.124: icmp_seq=2 ttl=64 time=0.094 ms
64 bytes from 192.168.0.124: icmp_seq=3 ttl=64 time=0.065 ms
64 bytes from 192.168.0.124: icmp_seq=4 ttl=64 time=0.065 ms
64 bytes from 192.168.0.124: icmp_seq=5 ttl=64 time=0.070 ms
64 bytes from 192.168.0.124: icmp_seq=6 ttl=64 time=0.074 ms
^C
--- 192.168.0.124 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5032ms
rtt min/avg/max/mdev = 0.060/0.071/0.094/0.013 ms
```
Удаление адреса.

Перед переходом к следующему пункту, нужно удалить IP из интерфейса, что мы добавили ранее.
```
[ValkyRa@centos ~]$ sudo ip addr del 192.168.0.124/24 dev enp0s3
```
Проверяем и видим, что ранее добавленный IP удалён из интерфейса.
```
[ValkyRa@centos ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.0.106/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s3
       valid_lft 5659sec preferred_lft 5659sec
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```

### 2. using centos network configuration file (statefull)
Открываем файлик, в котром хранится конфиг сетей
```
[ValkyRa@centos ~]$ sudo emacs /etc/sysconfig/network-scripts/ifcfg-enp0s3 
[sudo] пароль для ValkyRa: 
```
И добавляем в конец файла строку:
```
IPADDR="192.168.0.110"
```
Сохраняем файл и перезапускаем службы сетей
```
[ValkyRa@centos ~]$ sudo systemctl restart network
[sudo] пароль для ValkyRa: 
```
После же проверяем сети:
```
[ValkyRa@centos ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.0.106/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s3
       valid_lft 7181sec preferred_lft 7181sec
    inet 192.168.0.110/24 brd 192.168.0.255 scope global secondary noprefixroute enp0s3
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```
Ради интереса, пинганём новый IP
```
[ValkyRa@centos ~]$ ping 192.168.0.110
PING 192.168.0.110 (192.168.0.110) 56(84) bytes of data.
64 bytes from 192.168.0.110: icmp_seq=1 ttl=64 time=0.054 ms
64 bytes from 192.168.0.110: icmp_seq=2 ttl=64 time=0.035 ms
64 bytes from 192.168.0.110: icmp_seq=3 ttl=64 time=0.073 ms
^C
--- 192.168.0.110 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2007ms
rtt min/avg/max/mdev = 0.035/0.054/0.073/0.015 ms
```
Удаление нового IP:
Повторно открываем файлик и удаляем строчку, что добавляли ранее и перезапускаем службу сетей
```
[ValkyRa@centos ~]$ sudo systemctl restart network
```
И проверяем сети:
```
[ValkyRa@centos ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.0.106/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s3
       valid_lft 7191sec preferred_lft 7191sec
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```

### 3. using nmcli utility (statefull)
```
[ValkyRa@centos ~]$ sudo nmcli con mod enp0s3 ipv4.addresses 192.168.0.120/24
[sudo] пароль для ValkyRa: 



[ValkyRa@centos ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.0.106/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s3
       valid_lft 5360sec preferred_lft 5360sec
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
[ValkyRa@centos ~]$ sudo nmcli con mod enp0s3 ipv4.addresses 192.168.0.120/24
[ValkyRa@centos ~]$ sudo nmcli con up enp0s3
Соединение успешно активировано (адрес действующего D-Bus: /org/freedesktop/NetworkManager/ActiveConnection/6)
```
Проверяем сети:
```
[ValkyRa@centos ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.0.106/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s3
       valid_lft 7188sec preferred_lft 7188sec
    inet 192.168.0.120/24 brd 192.168.0.255 scope global secondary noprefixroute enp0s3
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```
Теперь проверим конфиг файла, чтобы убедиться, что данные нового IP там находятся
```
[ValkyRa@centos ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=ceac4c35-f1d6-4dd1-9089-789882eee972
DEVICE=enp0s3
ONBOOT=yes
IPADDR=192.168.0.120
PREFIX=24
```
Удаляем этот IP, путём изменения конфига:
```
[ValkyRa@centos ~]$ sudo emacs /etc/sysconfig/network-scripts/ifcfg-enp0s3
```
После изменения файла, перезапускаем службу и проверяем сети:
```
[ValkyRa@centos ~]$ sudo systemctl restart network
[ValkyRa@centos ~]$ ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 192.168.0.106/24 brd 192.168.0.255 scope global noprefixroute dynamic enp0s3
       valid_lft 7197sec preferred_lft 7197sec
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
```

## 2. You should have a possibility to use ssh client to connect to your node using new address from previous step. Run tcpdump in separate tmux session or separate connection before starting ssh client and capture packets that are related to this ssh connection. Find packets that are related to TCP session establish.

В первом терминале ввожу эту команду
```
[ValkyRa@centos ~]$ sudo tcpdump -vv -i enp0s3 dst 192.168.0.120 -w tcpdump_hw.rcap
[sudo] пароль для ValkyRa: 
tcpdump: listening on enp0s3, link-type EN10MB (Ethernet), capture size 262144 bytes
```
В этот момент использую PuTTY для подключения:

![image](https://user-images.githubusercontent.com/15772109/147661767-e89daafd-9635-4320-aedd-27a203d14729.png)

И во втором терминале ввожу эту команду, для чтения уже результата:
```
[ValkyRa@centos ~]$ tcpdump -x -r tcpdump_hw.rcap 
reading from file tcpdump_hw.rcap, link-type EN10MB (Ethernet)
```
Эти пакеты мы ловим в тот момент, когда мы ввели верный пароль и подключились по SSH:
```
15:48:17.789905 IP 192.168.0.161.64375 > centos.ssh: Flags [P.], seq 1604:1876, ack 1862, win 63984, length 272
15:48:17.853024 IP 192.168.0.161.64375 > centos.ssh: Flags [P.], seq 1876:1956, ack 1910, win 63936, length 80
15:48:18.089297 IP 192.168.0.161.64375 > centos.ssh: Flags [.], ack 2438, win 63408, length 0
15:48:18.089987 IP 192.168.0.161.64375 > centos.ssh: Flags [P.], seq 1956:2132, ack 2502, win 63344, length 176
15:48:18.099130 IP 192.168.0.161.64375 > centos.ssh: Flags [.], ack 2758, win 63088, length 0
15:48:18.351556 IP 192.168.0.161.64375 > centos.ssh: Flags [.], ack 2870, win 62976, length 0
```
## 3. Close session. Find in tcpdump output packets that are related to TCP session closure.

Прожимаем logout в Putty и в первом терминале останавливаем прослушку подключении и открываем снова файл с записями пакетов и получаем следующие пакеты:
```
[ValkyRa@centos ~]$ sudo tcpdump -vv -i enp0s3 dst 192.168.0.120 -w tcpdump_hw.rcap
[sudo] пароль для ValkyRa: 
tcpdump: listening on enp0s3, link-type EN10MB (Ethernet), capture size 262144 bytes
^C42 packets captured
42 packets received by filter
0 packets dropped by kernel
```
И открываем результат ещё раз:
```
[ValkyRa@centos ~]$ tcpdump -x -r tcpdump_hw.rcap 
reading from file tcpdump_hw.rcap, link-type EN10MB (Ethernet)
15:49:07.501242 IP 192.168.0.161.64375 > centos.ssh: Flags [P.], seq 2132:2196, ack 2870, win 62976, length 64
15:49:07.542879 IP 192.168.0.161.64375 > centos.ssh: Flags [.], ack 2966, win 62880, length 0
15:49:08.195342 IP 192.168.0.161.64375 > centos.ssh: Flags [P.], seq 2196:2260, ack 2966, win 62880, length 64
15:49:08.214476 IP 192.168.0.161.64375 > centos.ssh: Flags [.], ack 3206, win 64240, length 0
15:49:08.215202 IP 192.168.0.161.64375 > centos.ssh: Flags [F.], seq 2260, ack 3206, win 64240, length 0
15:49:08.218701 IP 192.168.0.161.64375 > centos.ssh: Flags [.], ack 3207, win 64240, length 0
```

## 4. run tcpdump and request any http site in separate session. Find HTTP request and answer packets with ASCII data in it.  Tcpdump command must be as strict as possible to capture only needed packages for this http request.
```
[ValkyRa@centos ~]$ sudo tcpdump -nn -A -i enp0s3 "(dst google.com and port 80 and host 192.168.0.106 and ip)"
[sudo] пароль для ValkyRa: 
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp0s3, link-type EN10MB (Ethernet), capture size 262144 bytes
```
После запуска на первом терминале команду выше, запускаю второй терминал и пишу:
```
[ValkyRa@centos ~]$ curl -L google.com
```
Без –L не идёт редирект на страницу. 

После же в первом терминале ловим пакеты:
```
13:57:14.748682 IP 192.168.0.106.49516 > 142.251.1.100.80: Flags [S], seq 1087930724, win 29200, options [mss 1460,sackOK,TS val 45078 ecr 0,nop,wscale 7], length 0
E..<1r@.@......j...d.l.P@..d......r.Q..........
............
13:57:14.755681 IP 192.168.0.106.49516 > 142.251.1.100.80: Flags [.], ack 1800168646, win 229, options [nop,nop,TS val 45085 ecr 1295465100], length 0
E..41s@.@......j...d.l.P@..ekLd.....Q......
....M7:.
13:57:14.755951 IP 192.168.0.106.49516 > 142.251.1.100.80: Flags [P.], seq 0:74, ack 1, win 229, options [nop,nop,TS val 45085 ecr 1295465100], length 74: HTTP: GET / HTTP/1.1
E..~1t@.@......j...d.l.P@..ekLd.....Q......
....M7:.GET / HTTP/1.1
User-Agent: curl/7.29.0
Host: google.com
Accept: */*


13:57:14.893028 IP 192.168.0.106.49516 > 142.251.1.100.80: Flags [.], ack 529, win 237, options [nop,nop,TS val 45216 ecr 1295465237], length 0
E..41u@.@......j...d.l.P@...kLf.....Q......
....M7;.
13:57:15.291487 IP 192.168.0.106.49516 > 142.251.1.100.80: Flags [F.], seq 74, ack 529, win 237, options [nop,nop,TS val 45621 ecr 1295465237], length 0
E..41v@.@......j...d.l.P@...kLf.....Q......
...5M7;.
13:57:15.299476 IP 192.168.0.106.49516 > 142.251.1.100.80: Flags [.], ack 530, win 237, options [nop,nop,TS val 45629 ecr 1295465642], length 0
E..41w@.@......j...d.l.P@...kLf.....Q......
...=M7<.
^C
6 packets captured
10 packets received by filter
0 packets dropped by kernel
```
