
# Домашнє завдання №3  
## Процеси та ресурси

---

## Завдання 1. Огляд активних процесів

---

```bash
support@maincopy:~$ ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0  22212 13624 ?        Ss   Mar05   3:38 /usr/lib/systemd/systemd --system --deserialize=82
root           2  0.0  0.0      0     0 ?        S    Mar05   0:02 [kthreadd]
root           3  0.0  0.0      0     0 ?        S    Mar05   0:00 [pool_workqueue_release]
...
support@maincopy:~$ htop
```
<img width="1489" height="198" alt="image" src="https://github.com/user-attachments/assets/faeea324-d88d-4d47-943c-2d3057591a01" />

keybord: shift+M

<img width="1178" height="199" alt="image" src="https://github.com/user-attachments/assets/7a0378e4-23e6-4bdd-8894-02095f37a333" />

```bash
support@maincopy:~$ ps -p $$
    PID TTY          TIME CMD
 174663 pts/1    00:00:00 bash
```
---

## Завдання 2. Робота у фоні та керування процесами

---

```bash
support@maincopy:~$ sleep 1000 &
[1] 174696
support@maincopy:~$ jobs
[1]+  Running                 sleep 1000 &
support@maincopy:~$ fg %1
sleep 1000
^Z
[1]+  Stopped                 sleep 1000
support@maincopy:~$ kill %1

[1]+  Stopped                 sleep 1000
support@maincopy:~$ jobs
[1]+  Terminated              sleep 1000
support@maincopy:~$ nohup sleep 500 &
[1] 174849
support@maincopy:~$ nohup: ignoring input and appending output to 'nohup.out'

support@maincopy:~$ exit

support@maincopy:~$ ls
Downloads  lab2  nohup.out  test_dir
support@maincopy:~$ ps aux | grep sleep
support   174849  0.0  0.0   5684  2128 ?        S    07:56   0:00 sleep 500
```

---

## Завдання 3. Пріоритети та обмеження

---

```bash
support@maincopy:~$ nice -n 10 sleep 500 &
[1] 174956
support@maincopy:~$ ps -l
F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 S  1000  174909  174908  0  80   0 -  2291 do_wai pts/2    00:00:00 bash
0 S  1000  174956  174909  0  90  10 -  1421 hrtime pts/2    00:00:00 sleep
0 R  1000  174957  174909 99  80   0 -  2762 -      pts/2    00:00:00 ps
support@maincopy:~$ renice +15 -p 174956
174956 (process ID) old priority 10, new priority 15
support@maincopy:~$ ps -l
F S   UID     PID    PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 S  1000  174909  174908  0  80   0 -  2291 do_wai pts/2    00:00:00 bash
0 S  1000  174956  174909  0  95  15 -  1421 hrtime pts/2    00:00:00 sleep
0 R  1000  174961  174909 99  80   0 -  2762 -      pts/2    00:00:00 ps
support@maincopy:~$ ulimit -a
real-time non-blocking time  (microseconds, -R) unlimited
core file size              (blocks, -c) 0
data seg size               (kbytes, -d) unlimited
scheduling priority                 (-e) 0
file size                   (blocks, -f) unlimited
pending signals                     (-i) 63646
max locked memory           (kbytes, -l) 2047132
max memory size             (kbytes, -m) unlimited
open files                          (-n) 1024
pipe size                (512 bytes, -p) 8
POSIX message queues         (bytes, -q) 819200
real-time priority                  (-r) 0
stack size                  (kbytes, -s) 8192
cpu time                   (seconds, -t) unlimited
max user processes                  (-u) 63646
virtual memory              (kbytes, -v) unlimited
file locks                          (-x) unlimited
```
---

## Завдання 4. Моніторинг ресурсів

---

```bash
support@maincopy:~$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              1.6G  1.4M  1.6G   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   97G  7.9G   85G   9% /
tmpfs                              7.9G     0  7.9G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          2.0G  200M  1.6G  11% /boot
tmpfs                              1.6G   12K  1.6G   1% /run/user/1000
support@maincopy:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:            15Gi       1.1Gi        11Gi       1.4Mi       3.6Gi        14Gi
Swap:          4.0Gi          0B       4.0Gi
```
