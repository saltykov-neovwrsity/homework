# Домашнє завдання №7
## Операційна система і контейнери

### Варіант A — аналіз життєвого циклу контейнера

Для виконання завдання використано Docker-контейнер з образом `python:3.12-slim` та простим HTTP-сервером Python.

## Перевірка Docker

Перевіряємо встановлену версію Docker:

```bash
support@maincopy:~$ docker --version
Docker version 29.1.3, build 29.1.3-0ubuntu3~24.04.2
```

Перевіряємо запущені контейнери:

```bash
support@maincopy:~$ sudo docker ps
CONTAINER ID   IMAGE                     COMMAND                  CREATED        STATUS        PORTS                                         NAMES
e408e9b7e2e6   umt-pythonweb-hw-11-web   "sh -c 'sleep 5 && a…"   5 days ago     Up 5 days     0.0.0.0:8000->8000/tcp, [::]:8000->8000/tcp   contacts_web
839c9754d940   postgres:16               "docker-entrypoint.s…"   4 weeks ago    Up 4 weeks    0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp   contacts_postgres
83e4197cf020   redis:alpine              "docker-entrypoint.s…"   4 weeks ago    Up 4 weeks    0.0.0.0:6379->6379/tcp, [::]:6379->6379/tcp   contacts_redis
f5099df6800d   mysql:5.6                 "docker-entrypoint.s…"   3 months ago   Up 3 months   0.0.0.0:3306->3306/tcp, [::]:3306->3306/tcp   mysql56
```

Команди керування контейнерами запускаються через `sudo`, оскільки звичайний користувач не має прямого доступу до Docker socket.

## Запуск контейнера

Запускаємо контейнер з HTTP-сервером Python:

```bash
support@maincopy:~$ sudo docker run -d --name module7-python -p 8080:8080 python:3.12-slim python -u -m http.server 8080 --bind 0.0.0.0
d3e6601da61a9cac34048bac020563687aaa479366ea5412482533a3515932aa
```

Параметр `-d` запускає контейнер у фоновому режимі. Параметр `--name` задає ім’я контейнера, а `-p 8080:8080` публікує порт `8080` контейнера на порту `8080` хоста.

Команда:

```text
python -u -m http.server 8080 --bind 0.0.0.0
```

запускає простий HTTP-сервер, який слухає всі мережеві інтерфейси контейнера.

## Перевірка процесу PID 1

Перевіряємо процес з PID 1 усередині контейнера:

```bash
support@maincopy:~$ sudo docker exec module7-python sh -c 'grep -E "^(Name|Pid|PPid):" /proc/1/status'
Name:   python
Pid:    1
PPid:   0
```

Процес `python` має PID 1, тому що саме ця команда була передана Docker під час запуску контейнера. Вона є головним процесом контейнера.

Перевіряємо процес з боку хостової операційної системи:

```bash
support@maincopy:~$ sudo docker top module7-python
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                522653              522630              2                   19:46               ?                   00:00:00            python -u -m http.server 8080 --bind 0.0.0.0
```

Усередині контейнера процес має PID 1, але на хості цей самий процес має PID `522653`. Це пояснюється окремим PID namespace контейнера.

Контейнер працює доти, доки працює його головний процес PID 1. Якщо цей процес завершується, контейнер також переходить у стан `Exited`.

## Перевірка доступності HTTP-сервера

Виконуємо HTTP-запит з хоста:

```bash
support@maincopy:~$ curl http://127.0.0.1:8080 >/dev/null
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   877  100   877    0     0   104k      0 --:--:-- --:--:-- --:--:--  107k
```

Сервер повернув відповідь розміром `877` байт. Це підтверджує, що HTTP-сервіс працює і доступний через опублікований порт `8080`.

## Перевірка логів

Переглядаємо логи контейнера:

```bash
support@maincopy:~$ sudo docker logs module7-python
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
172.17.0.1 - - [26/Jun/2026 19:47:34] "GET / HTTP/1.1" 200 -
```

Перший рядок показує запуск HTTP-сервера. Другий рядок з’явився після виконання запиту через `curl`.

Команда `docker logs` показує дані, які головний процес контейнера записує у стандартні потоки `stdout` і `stderr`. Docker збирає ці потоки та зберігає їх через налаштований logging driver.

Адреса `172.17.0.1` у записі логу є адресою хоста в стандартній bridge-мережі Docker. Саме з неї запит потрапив до контейнера.

## Перевірка стану контейнера

Перевіряємо, що контейнер працює:

```bash
support@maincopy:~$ sudo docker ps --filter name=module7-python
CONTAINER ID   IMAGE              COMMAND                  CREATED              STATUS              PORTS                                         NAMES
d3e6601da61a   python:3.12-slim   "python -u -m http.s…"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp   module7-python
```

У полі `STATUS` вказано `Up`, а в полі `PORTS` видно перенаправлення порту `8080` хоста на порт `8080` контейнера.

## Зупинка контейнера

Зупиняємо контейнер командою `docker stop`:

```bash
support@maincopy:~$ sudo docker stop module7-python
module7-python
```

Перевіряємо стан:

```bash
support@maincopy:~$ sudo docker ps -a --filter name=module7-python
CONTAINER ID   IMAGE              COMMAND                  CREATED              STATUS                       PORTS     NAMES
d3e6601da61a   python:3.12-slim   "python -u -m http.s…"   About a minute ago   Exited (137) 3 seconds ago             module7-python
```

Додатково перевіряємо стан і код завершення:

```bash
support@maincopy:~$ sudo docker inspect module7-python --format 'Status={{.State.Status}} ExitCode={{.State.ExitCode}}'
Status=exited ExitCode=137
```

Під час виконання `docker stop` Docker спочатку надсилає головному процесу контейнера сигнал `SIGTERM`. Процес отримує час для коректного завершення.

У цьому випадку контейнер завершився з кодом `137`. Код `137` дорівнює `128 + 9`, де `9` — номер сигналу `SIGKILL`. Це означає, що процес не завершився після `SIGTERM` у межах тайм-ауту, тому Docker примусово завершив його сигналом `SIGKILL`.

`SIGKILL` не можна перехопити або проігнорувати. Після завершення процесу PID 1 контейнер перейшов у стан `Exited`.

## Повторний запуск контейнера

Повторно запускаємо контейнер:

```bash
support@maincopy:~$ sudo docker start module7-python
module7-python
```

Перевіряємо стан:

```bash
support@maincopy:~$ sudo docker ps --filter name=module7-python
CONTAINER ID   IMAGE              COMMAND                  CREATED              STATUS         PORTS                                         NAMES
d3e6601da61a   python:3.12-slim   "python -u -m http.s…"   About a minute ago   Up 7 seconds   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp   module7-python
```

Після повторного запуску Docker знову запустив основну команду контейнера.

Перевіряємо PID 1:

```bash
support@maincopy:~$ sudo docker exec module7-python sh -c 'grep -E "^(Name|Pid|PPid):" /proc/1/status'
Name:   python
Pid:    1
PPid:   0
```

Процес Python знову є головним процесом із PID 1.

## Спроба надіслати сигнал процесу

Виконуємо команду:

```bash
support@maincopy:~$ sudo docker exec module7-python kill -TERM 1
OCI runtime exec failed: exec failed: unable to start container process: exec: "kill": executable file not found in $PATH
```

Команда не виконалась, оскільки в мінімальному образі `python:3.12-slim` немає окремого виконуваного файла `kill` у змінній `PATH`.

Ця помилка не завершила головний процес контейнера. Перевіряємо стан:

```bash
support@maincopy:~$ sudo docker ps -a --filter name=module7-python
CONTAINER ID   IMAGE              COMMAND                  CREATED         STATUS          PORTS                                         NAMES
d3e6601da61a   python:3.12-slim   "python -u -m http.s…"   2 minutes ago   Up 32 seconds   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp   module7-python
```

Перевіряємо стан через `docker inspect`:

```bash
support@maincopy:~$ sudo docker inspect module7-python --format 'Status={{.State.Status}} ExitCode={{.State.ExitCode}}'
Status=running ExitCode=0
```

Контейнер продовжує працювати, тому що сигнал процесу PID 1 фактично не був надісланий.

## Спроба видалення запущеного контейнера

Виконуємо видалення контейнера:

```bash
support@maincopy:~$ sudo docker rm module7-python
Error response from daemon: cannot remove container "module7-python": container is running: stop the container before removing or force remove
```

Docker не дозволяє звичайною командою `docker rm` видалити контейнер, який ще працює. Спочатку його потрібно зупинити або використати примусове видалення.

## Висновок

Контейнер є не окремою віртуальною машиною, а ізольованим Linux-процесом із власними namespace та файловою системою.

У цьому контейнері головним процесом є:

```text
python -u -m http.server 8080 --bind 0.0.0.0
```

Усередині контейнера цей процес має PID 1, а на хості — інший PID. Життя контейнера безпосередньо залежить від головного процесу: поки PID 1 працює, контейнер має стан `running`; після завершення PID 1 контейнер переходить у стан `exited`.

Команда `docker stop` спочатку надсилає процесу сигнал `SIGTERM`. Якщо процес не завершується протягом тайм-ауту, Docker надсилає `SIGKILL`. У виконаному тесті це підтверджується кодом завершення `137`.

Логи контейнера беруться зі стандартних потоків `stdout` і `stderr` головного процесу. Запуск сервера та HTTP-запит були відображені командою `docker logs`.

Port mapping `8080:8080` зробив HTTP-сервер доступним з хоста за адресою:

```text
http://127.0.0.1:8080
```

Таким чином, під час виконання завдання було перевірено зв’язок між контейнером і процесом PID 1, роботу сигналів завершення, отримання логів та доступ до сервісу через опублікований порт.

