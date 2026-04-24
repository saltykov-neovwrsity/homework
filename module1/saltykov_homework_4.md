# Домашнє завдання №4  
## Пакети, сервіси та журнали

---

## Завдання 1. Менеджери пакетів

```bash
support@maincopy:~$ sudo apt update
[sudo] password for support:
Hit:1 http://ua.archive.ubuntu.com/ubuntu noble InRelease
Hit:2 http://ua.archive.ubuntu.com/ubuntu noble-updates InRelease
Hit:3 http://ua.archive.ubuntu.com/ubuntu noble-backports InRelease
Hit:4 http://security.ubuntu.com/ubuntu noble-security InRelease
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
...

support@maincopy:~$ tree --version
tree v2.1.1 © 1996 - 2023 by Steve Baker, Thomas Moore, Francesc Rocher, Florian Sesser, Kyosuke Tokoro
support@maincopy:~$ sudo apt remove tree -y
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  tree
0 upgraded, 0 newly installed, 1 to remove and 39 not upgraded.
After this operation, 111 kB disk space will be freed.
(Reading database ... 130418 files and directories currently installed.)
Removing tree (2.1.1-2ubuntu3.24.04.2) ...
Processing triggers for man-db (2.12.0-4build2) ...
```

---

## Завдання 2. Керування сервісами через systemctl

---

```bash
support@maincopy:~$ systemctl status cron
● cron.service - Regular background program processing daemon
     Loaded: loaded (/usr/lib/systemd/system/cron.service; enabled; preset: enabled)
     Active: active (running) since Thu 2026-03-05 10:35:42 UTC; 1 month 19 days ago
       Docs: man:cron(8)
   Main PID: 1132 (cron)
      Tasks: 1 (limit: 19093)
     Memory: 704.0K (peak: 4.0M)
        CPU: 3min 47.902s
     CGroup: /system.slice/cron.service
             └─1132 /usr/sbin/cron -f -P
...

support@maincopy:~$ sudo systemctl stop cron
support@maincopy:~$ systemctl status cron
○ cron.service - Regular background program processing daemon
     Loaded: loaded (/usr/lib/systemd/system/cron.service; enabled; preset: enabled)
     Active: inactive (dead) since Fri 2026-04-24 08:48:51 UTC; 10s ago
   Duration: 1month 2w 5d 11h 43min 8.678s
       Docs: man:cron(8)
    Process: 1132 ExecStart=/usr/sbin/cron -f -P $EXTRA_OPTS (code=killed, signal=TERM)
   Main PID: 1132 (code=killed, signal=TERM)
        CPU: 3min 47.903s
...
Apr 24 08:48:51 maincopy systemd[1]: Stopping cron.service - Regular background program processing daemon...
Apr 24 08:48:51 maincopy systemd[1]: cron.service: Deactivated successfully.
Apr 24 08:48:51 maincopy systemd[1]: Stopped cron.service - Regular background program processing daemon.
Apr 24 08:48:51 maincopy systemd[1]: cron.service: Consumed 3min 47.903s CPU time, 4.0M memory peak, 0B memory swap peak.
support@maincopy:~$ sudo systemctl start cron
support@maincopy:~$ systemctl status cron
● cron.service - Regular background program processing daemon
     Loaded: loaded (/usr/lib/systemd/system/cron.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-04-24 08:49:20 UTC; 2s ago
       Docs: man:cron(8)
   Main PID: 176009 (cron)
      Tasks: 1 (limit: 19093)
     Memory: 344.0K (peak: 460.0K)
        CPU: 14ms
     CGroup: /system.slice/cron.service
             └─176009 /usr/sbin/cron -f -P

Apr 24 08:49:20 maincopy systemd[1]: Started cron.service - Regular background program processing daemon.
Apr 24 08:49:20 maincopy (cron)[176009]: cron.service: Referenced but unset environment variable evaluates to an empty string: EXTRA_OPTS
Apr 24 08:49:20 maincopy cron[176009]: (CRON) INFO (pidfile fd = 3)
Apr 24 08:49:20 maincopy cron[176009]: (CRON) INFO (Skipping @reboot jobs -- not system startup)
support@maincopy:~$ sudo systemctl enable cron
Synchronizing state of cron.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable cron
```

---

## Завдання 3. Робота з логами

---

```bash
support@maincopy:~$ cd /var/log
support@maincopy:/var/log$ tail -n 10 syslog
2026-04-24T08:56:13.478706+00:00 maincopy cron[176189]: (CRON) INFO (Skipping @reboot jobs -- not system startup)
2026-04-24T08:56:16.891046+00:00 maincopy systemd[1]: Reloading requested from client PID 176197 ('systemctl') (unit session-8569.scope)...
2026-04-24T08:56:16.891535+00:00 maincopy systemd[1]: Reloading...
2026-04-24T08:56:17.564726+00:00 maincopy systemd[1]: Reloading finished in 672 ms.
2026-04-24T08:56:17.667097+00:00 maincopy systemd[1]: Reloading requested from client PID 176245 ('systemctl') (unit session-8569.scope)...
2026-04-24T08:56:17.667297+00:00 maincopy systemd[1]: Reloading...
2026-04-24T08:56:18.156904+00:00 maincopy systemd[1]: Reloading finished in 488 ms.
2026-04-24T08:56:18.233173+00:00 maincopy systemd[1]: Reloading requested from client PID 176193 ('systemctl') (unit session-8569.scope)...
2026-04-24T08:56:18.233381+00:00 maincopy systemd[1]: Reloading...
2026-04-24T08:56:18.766825+00:00 maincopy systemd[1]: Reloading finished in 532 ms.
support@maincopy:/var/log$ journalctl -p err
Mar 05 10:34:55 maincopy kernel: ITS: WARNING: ITS mitigation depends on retpoline and rethunk support
Mar 05 10:34:56 maincopy kernel: piix4_smbus 0000:00:07.3: SMBus Host Controller not enabled!
Mar 05 10:39:25 maincopy login[1167]: PAM unable to dlopen(pam_lastlog.so): /usr/lib/security/pam_lastlog.so: cannot open shared object file: No such file or directory
Mar 05 10:39:25 maincopy login[1167]: PAM adding faulty module: pam_lastlog.so
Mar 12 11:23:55 maincopy login[1398]: PAM unable to dlopen(pam_lastlog.so): /usr/lib/security/pam_lastlog.so: cannot open shared object file: No such file or directory
Mar 12 11:23:55 maincopy login[1398]: PAM adding faulty module: pam_lastlog.so
support@maincopy:/var/log$ journalctl -u cron | grep -Ei 'started|stopped' | tail -n 5
Mar 05 10:35:42 maincopy systemd[1]: Started cron.service - Regular background program processing daemon.
Apr 24 08:48:51 maincopy systemd[1]: Stopped cron.service - Regular background program processing daemon.
Apr 24 08:49:20 maincopy systemd[1]: Started cron.service - Regular background program processing daemon.
```
---

## Завдання 4. Створення власного сервісу

---
   
```bash
support@maincopy:~$ nano date_logger.sh
  GNU nano 7.2                                                                          date_logger.sh
#!/bin/bash
while true
do
  echo "Current date: $(date)" >> /home/support/date_log.txt
  sleep 1
done

support@maincopy:~$ chmod +x date_logger.sh
support@maincopy:~$ sudo nano /etc/systemd/system/myscript.service
[sudo] password for support:
  GNU nano 7.2                                                               /etc/systemd/system/myscript.service *
[Unit]
Description=Every secon logger
After=network.target

[Service]
User=support
WorkingDirectory=/home/support
ExecStart=/bin/bash /home/support/date_logger.sh
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target

support@maincopy:~$ sudo systemctl daemon-reload
support@maincopy:~$ sudo systemctl start myscript.service
support@maincopy:~$ systemctl status myscript.service
● myscript.service - Every secon logger
     Loaded: loaded (/etc/systemd/system/myscript.service; disabled; preset: enabled)
     Active: active (running) since Fri 2026-04-24 09:56:41 UTC; 3s ago
   Main PID: 176737 (bash)
...

support@maincopy:~$ ls
date_logger.sh  date_log.txt  Downloads  lab2  nohup.out  test_dir
support@maincopy:~$ tail -f /home/support/date_log.txt
Current date: Fri Apr 24 09:57:14 AM UTC 2026
Current date: Fri Apr 24 09:57:15 AM UTC 2026
Current date: Fri Apr 24 09:57:16 AM UTC 2026
Current date: Fri Apr 24 09:57:17 AM UTC 2026
Current date: Fri Apr 24 09:57:18 AM UTC 2026
Current date: Fri Apr 24 09:57:19 AM UTC 2026
Current date: Fri Apr 24 09:57:20 AM UTC 2026
...
^C
support@maincopy:~$ sudo systemctl stop myscript.service
```
