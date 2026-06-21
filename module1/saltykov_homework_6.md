# Домашнє завдання №6
## Автоматизація і Shell-скрипти

---

## Завдання 1. Мережева діагностика

Було виконано команду ip a.

Основний мережевий інтерфейс: ens160
Локальна IP-адреса інтерфейсу: 192.168.8.34/26

Також на сервері присутні docker-інтерфейси:
docker0 — 172.17.0.1/16, а також інші службові та docker інтерфейси

Доступ до інтернету перевірено командою:

ping 8.8.8.8

Результат: доступ до інтернету є, оскільки отримано відповіді від 8.8.8.8.
Втрат пакетів немає: 0% packet loss.

Командою ss -tulpn перевірено відкриті listening-порти.

Приклади сервісів/портів, які слухають з'єднання:

22/tcp — SSH

3306/tcp — MySQL

5432/tcp — PostgreSQL

6379/tcp — Redis

8000/tcp — вебсервіс

53/tcp і 53/udp — DNS resolver

```bash

support@maincopy:~$ ip a
1: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:fc:93:79 brd ff:ff:ff:ff:ff:ff
    altname enp3s0
    inet 192.168.8.34/26 metric 100 brd 192.168.8.63 scope global dynamic ens160
       valid_lft 445403sec preferred_lft 445403sec
    inet6 fe80::20c:29ff:fefc:9379/64 scope link
       valid_lft forever preferred_lft forever
2: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 66:77:a1:05:14:89 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::6477:a1ff:fe05:1489/64 scope link
       valid_lft forever preferred_lft forever

...

support@maincopy:~$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=14.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=14.3 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=115 time=14.1 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=115 time=14.1 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=115 time=14.1 ms
^C
--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4006ms
rtt min/avg/max/mdev = 14.080/14.208/14.436/0.132 ms

support@maincopy:~$ ss -tulpn
Netid             State              Recv-Q             Send-Q                               Local Address:Port                          Peer Address:Port            Process
udp               UNCONN             0                  0                                       127.0.0.54:53                                 0.0.0.0:*
udp               UNCONN             0                  0                                    127.0.0.53%lo:53                                 0.0.0.0:*
udp               UNCONN             0                  0                              192.168.8.34%ens160:68                                 0.0.0.0:*
tcp               LISTEN             0                  4096                                       0.0.0.0:3306                               0.0.0.0:*
tcp               LISTEN             0                  4096                                     127.0.0.1:32821                              0.0.0.0:*
tcp               LISTEN             0                  4096                                       0.0.0.0:22                                 0.0.0.0:*
tcp               LISTEN             0                  4096                                    127.0.0.54:53                                 0.0.0.0:*
tcp               LISTEN             0                  4096                                       0.0.0.0:8000                               0.0.0.0:*
tcp               LISTEN             0                  4096                                       0.0.0.0:6379                               0.0.0.0:*
tcp               LISTEN             0                  4096                                       0.0.0.0:5432                               0.0.0.0:*
tcp               LISTEN             0                  4096                                 127.0.0.53%lo:53                                 0.0.0.0:*
tcp               LISTEN             0                  4096                                          [::]:3306                                  [::]:*
tcp               LISTEN             0                  4096                                          [::]:22                                    [::]:*
tcp               LISTEN             0                  4096                                          [::]:8000                                  [::]:*
tcp               LISTEN             0                  4096                                          [::]:6379                                  [::]:*
tcp               LISTEN             0                  4096                                          [::]:5432                                  [::]:*

```

---

## Завдання 2. SSH-доступ з ключами та config

---

Для тестування розгорнув ще одну віртуальну машину на Ubuntu 24.04. IP адреса: 192.168.8.36/26

Linux-клієнт: maincopy
IP: 192.168.8.34

Linux-сервер: testforumt
IP: 192.168.8.36


Виконую на maincopy:

```bash

support@maincopy:~$ ping 192.168.8.36
PING 192.168.8.36 (192.168.8.36) 56(84) bytes of data.
64 bytes from 192.168.8.36: icmp_seq=1 ttl=64 time=0.332 ms
64 bytes from 192.168.8.36: icmp_seq=2 ttl=64 time=0.306 ms
^C
--- 192.168.8.36 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1042ms

support@maincopy:~$ ls ~/.ssh
authorized_keys

support@maincopy:~$ ssh-keygen -t ed25519
Generating public/private ed25519 key pair.
...

support@maincopy:~$ ls ~/.ssh
authorized_keys  id_ed25519  id_ed25519.pub

support@maincopy:~$ ssh-copy-id support@192.168.8.36
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/support/.ssh/id_ed25519.pub"
...
Number of key(s) added: 1

support@maincopy:~$ nano ~/.ssh/config
support@maincopy:~$ cat .ssh/config
Host testforumt
        HostName 192.168.8.36
        User support
        IdentityFile ~/.ssh/id_ed25519

support@maincopy:~$ ssh testforumt
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.8.0-124-generic x86_64)
...

support@testforumt:~$

```

Підключення працює без пароля.

---

## Завдання 3. Копіювання файлів між машинами

---

```bash

support@maincopy:~$ echo "test" > test.txt
support@maincopy:~$ cat test.txt
test
support@maincopy:~$ scp test.txt testforumt:~/
test.txt                                                                                                                                               100%    5     5.1KB/s   00:00
support@maincopy:~$ ssh testforumt "cat ~/test.txt"
test

support@maincopy:~$ ssh testforumt "mkdir -p ~/sync_folder"
support@maincopy:~$ ssh testforumt "ls ~/"
sync_folder
test.txt
support@maincopy:~$ mkdir -p local_folder
support@maincopy:~$ echo "test 1" > local_folder/file1.txt
support@maincopy:~$ echo "test 2" > local_folder/file2.txt
support@maincopy:~$ ls local_folder
file1.txt  file2.txt

support@maincopy:~$ rsync -av local_folder/ testforumt:~/sync_folder/
sending incremental file list
./
file1.txt
file2.txt

sent 215 bytes  received 57 bytes  108.80 bytes/sec
total size is 14  speedup is 0.05
support@maincopy:~$ ssh testforumt "cat ~/sync_folder/file1.txt ~/sync_folder/file2.txt"
test 1
test 2

support@maincopy:~$ sftp testforumt
Connected to testforumt.
sftp> ls
sync_folder  test.txt
sftp> cd sync_folder
sftp> ls
file1.txt  file2.txt

```
---
