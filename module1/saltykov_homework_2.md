# Домашнє завдання №2  
## Файлова система і права доступу

---

## Завдання 1. Ієрархія каталогів Linux

```bash
support@maincopy:~$ cd / && ls
bin  bin.usr-is-merged  boot  cdrom  data  dev  etc  home  lib  lib64  lib.usr-is-merged  lost+found  media  mnt  opt  proc  root  run  sbin  sbin.usr-is-merged  snap  srv  swap.img  sys  tmp  usr  var
support@maincopy:/$ cd /etc && ls
adduser.conf            cron.d                dpkg          host.conf        ld.so.conf.d    magic.mime      network              pm           rsyslog.conf   subuid-            update-manager
alternatives            cron.daily            e2scrub.conf  hostname         legal           mailcap         networkd-dispatcher  polkit-1     rsyslog.d      sudo.conf          update-motd.d
apparmor                cron.hourly           environment   hosts            libaudit.conf   mailcap.order   NetworkManager       pollinate    screenrc       sudoers            update-notifier
apparmor.d              cron.monthly          ethertypes    hosts.allow      libblockdev     manpath.config  networks             profile      security       sudoers.d          UPower
apport                  crontab               fonts         hosts.deny       libibverbs.d    mc              newt                 profile.d    selinux        sudo_logsrvd.conf  usb_modeswitch.conf
apt                     cron.weekly           fstab         ImageMagick-6    libnl-3         mdadm           nftables.conf        protocols    sensors3.conf  supercat           usb_modeswitch.d
bash.bashrc             cron.yearly           fuse.conf     init             libpaper.d      mime.types      nsswitch.conf        python3      sensors.d      sysctl.conf        vconsole.conf
bash_completion         cryptsetup-initramfs  fwupd         init.d           locale.alias    mke2fs.conf     opt                  python3.12   services       sysctl.d           vim
bash_completion.d       crypttab              gai.conf      initramfs-tools  locale.conf     ModemManager    os-release           rc0.d        sgml           sysstat            vmware-tools
bindresvport.blacklist  dbus-1                ghostscript   inputrc          locale.gen      modprobe.d      overlayroot.conf     rc1.d        shadow         systemd            vtrgb
binfmt.d                debconf.conf          gnutls        iproute2         localtime       modules         PackageKit           rc2.d        shadow-        terminfo           wgetrc
byobu                   debian_version        groff         iscsi            logcheck        modules-load.d  pam.conf             rc3.d        shells         thermald           X11
ca-certificates         default               group         issue            login.defs      mtab            pam.d                rc4.d        skel           timezone           xattr.conf
ca-certificates.conf    deluser.conf          group-        issue.net        logrotate.conf  multipath       papersize            rc5.d        sos            tmpfiles.d         xdg
cloud                   depmod.d              grub.d        kernel           logrotate.d     multipath.conf  passwd               rc6.d        ssh            ubuntu-advantage   xml
cni                     dhcp                  gshadow       landscape        lsb-release     nanorc          passwd-              rcS.d        ssl            ucf.conf           zsh_command_not_found
console-setup           dhcpcd.conf           gshadow-      ldap             lvm             needrestart     perl                 resolv.conf  subgid         udev
credstore               dnsmasq.d             gss           ld.so.cache      machine-id      netconfig       pki                  rmt          subgid-        udisks2
credstore.encrypted     docker                hdparm.conf   ld.so.conf       magic           netplan         plymouth             rpc          subuid         ufw
support@maincopy:/etc$ cd /home && ls
support
```

---

## Завдання 2. Файли, каталоги та посилання

---

```bash
support@maincopy:/$ mkdir ~/lab2
support@maincopy:/$ touch ~/lab2/file.txt
support@maincopy:/$ cp ~/lab2/file.txt ~/lab2/file_copy.txt
support@maincopy:/$ mv ~/lab2/file_copy.txt ~/lab2/renamed_file.txt
support@maincopy:/$ ln ~/lab2/file.txt ~/lab2/hard_link.txt
support@maincopy:/$ ln -s ~/lab2/file.txt ~/lab2/symbolic_link.txt
support@maincopy:/$ find /home -name "file.txt"
/home/support/lab2/file.txt
support@maincopy:~/lab2$ ls ~/lab2
file.txt  hard_link.txt  renamed_file.txt  symbolic_link.txt
```

---

## Завдання 3. Права доступу

---

```bash
support@maincopy:/$ ls -l ~/lab2/file.txt
-rw-rw-r-- 2 support support 0 Apr 14 08:15 /home/support/lab2/file.txt
support@maincopy:/$ chmod 444 ~/lab2/file.txt
support@maincopy:/$ ls -l ~/lab2/file.txt
-r--r--r-- 2 support support 0 Apr 14 08:15 /home/support/lab2/file.txt
support@maincopy:/$ chmod u+w ~/lab2/file.txt
support@maincopy:/$ ls -l ~/lab2/file.txt
-rw-r--r-- 2 support support 0 Apr 14 08:15 /home/support/lab2/file.txt
support@maincopy:/$ umask
0002
support@maincopy:/$ umask 022
support@maincopy:/$ umask
0022
```
---

## Завдання 4. Користувачі

---

```bash
support@maincopy:/$ sudo adduser trainee
[sudo] password for support:
info: Adding user `trainee' ...
info: Selecting UID/GID from range 1000 to 59999 ...
info: Adding new group `trainee' (1001) ...
info: Adding new user `trainee' (1001) with group `trainee (1001)' ...
info: Creating home directory `/home/trainee' ...
info: Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for trainee
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] y
info: Adding new user `trainee' to supplemental / extra groups `users' ...
info: Adding user `trainee' to group `users' ...
support@maincopy:/$ sudo usermod -aG sudo trainee
support@maincopy:/$ grep "trainee" /etc/passwd
trainee:x:1001:1001:,,,:/home/trainee:/bin/bash
```
