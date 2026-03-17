# Домашнє завдання №1  
## Основи Linux і робота в терміналі  

---

## Завдання 1. Базові команди

```bash
support@maincopy:~$ pwd
/home/support
support@maincopy:~$ ls ~
support@maincopy:~$ ls
support@maincopy:~$ cd ~/Downloads
-bash: cd: /home/support/Downloads: No such file or directory
support@maincopy:~$ mkdir ~/Downloads
support@maincopy:~$ ls ~
Downloads
support@maincopy:~$ cd ~/Downloads
support@maincopy:~/Downloads$ pwd
/home/support/Downloads
support@maincopy:~/Downloads$ ls
support@maincopy:~/Downloads$ ls ~
Downloads
support@maincopy:~/Downloads$ cat > file.txt
support@maincopy:~/Downloads$ ls
file.txt
support@maincopy:~/Downloads$ cat file.txt
support@maincopy:~/Downloads$ cat >> file.txt
1
2
3
support@maincopy:~/Downloads$ cat file.txt
1
2
3
support@maincopy:~/Downloads$ cd /home/support
support@maincopy:~$ pwd
/home/support
support@maincopy:~$ cd ~/Downloads
support@maincopy:~/Downloads$ pwd
/home/support/Downloads
support@maincopy:~/Downloads$ cd ~
support@maincopy:~$ pwd
/home/support
```

---

## Завдання 2. Робота з документацією

---

```bash
support@maincopy:~$ man ls
support@maincopy:~$ help cd
cd: cd [-L|[-P [-e]] [-@]] [dir]
    Change the shell working directory.

    Change the current directory to DIR.  The default DIR is the value of the
    HOME shell variable. If DIR is "-", it is converted to $OLDPWD.
...
support@maincopy:~$ man cat
support@maincopy:~$ man man
support@maincopy:~$ cp --help
Usage: cp [OPTION]... [-T] SOURCE DEST
  or:  cp [OPTION]... SOURCE... DIRECTORY
  or:  cp [OPTION]... -t DIRECTORY SOURCE...
Copy SOURCE to DEST, or multiple SOURCE(s) to DIRECTORY.
...
support@maincopy:~$ mv --help
Usage: mv [OPTION]... [-T] SOURCE DEST
  or:  mv [OPTION]... SOURCE... DIRECTORY
  or:  mv [OPTION]... -t DIRECTORY SOURCE...
Rename SOURCE to DEST, or move SOURCE(s) to DIRECTORY.
...
```

---

### Питання

1. Який ключ ls показує приховані файли?

```bash
support@maincopy:~$ ls -a
.  ..  .bash_history  .bash_logout  .bashrc  .cache  .config  Downloads  .lesshst  .local  .profile  .ssh  .sudo_as_admin_successful
```

2. Який ключ у cat нумерує рядки?

```bash
support@maincopy:~$ cat -n ~/Downloads/file.txt
     1  1
     2  2
     3  3
```

3. Чим відрізняються man і -help?

   - Команда man відкриває повну документацію команди. Вона містить детальний опис, усі можливі параметри та пояснення їх використання. Відкривається у вигляді окремого переглядача, де можна шукати інформацію.
   - Команда --help показує коротку довідку прямо в терміналі. Вона містить лише основні параметри та приклади використання.

---

## Завдання 3. Міні-сценарій

---

```bash
support@maincopy:~$ cd ~
support@maincopy:~$ mkdir test_dir
support@maincopy:~$ cd test_dir
support@maincopy:~/test_dir$ cat > test2.txt
one
two
three
support@maincopy:~/test_dir$ ls
test2.txt
support@maincopy:~/test_dir$ cd ..
support@maincopy:~$ pwd
/home/support
support@maincopy:~$ man cat
support@maincopy:~$ cat -n ~/test_dir/test2.txt
     1  one
     2  two
     3  three
```



