# Домашнє завдання №6
## Автоматизація і Shell-скрипти

### Варіант B — скрипт деплою застосунку

Для перевірки роботи скрипта використано навчальний Python-застосунок, розташований у каталозі:

```text
/home/support/UMT-pythonweb-hw-11
```

## Перевірка вихідного каталогу

Перевіряємо вміст каталогу застосунку:

```bash
support@maincopy:~$ ls -la /home/support/UMT-pythonweb-hw-11
total 112
drwxrwxr-x  6 support support 4096 Jun 21 20:50 .
drwxr-x--- 14 support support 4096 Jun 21 20:48 ..
drwxrwxr-x  4 support support 4096 May 28 19:51 alembic
-rw-rw-r--  1 support support 5008 May 28 19:51 alembic.ini
-rw-rw-r--  1 support support 4162 May 28 19:51 auth_service.py
-rw-rw-r--  1 support support  702 May 28 19:51 config.py
-rw-rw-r--  1 support support 3957 May 28 19:51 crud.py
-rw-rw-r--  1 support support  396 May 28 19:51 database.py
-rw-rw-r--  1 support support  859 May 28 19:51 docker-compose.yml
-rw-rw-r--  1 support support  204 May 28 19:51 Dockerfile
-rw-rw-r--  1 support support 1182 May 28 19:51 email_service.py
-rw-rw-r--  1 support support  668 May 28 19:51 .env
-rw-rw-r--  1 support support  693 May 28 19:51 .env.example
-rw-rw-r--  1 support support  314 May 28 19:51 .gitignore
-rw-rw-r--  1 support support  870 May 28 19:51 main.py
-rw-rw-r--  1 support support 1217 May 28 19:51 models.py
drwxrwxr-x  2 support support 4096 May 28 19:51 __pycache__
-rw-rw-r--  1 support support 3839 May 28 19:51 README.md
-rw-rw-r--  1 support support  191 May 28 19:51 requirements.txt
-rw-rw-r--  1 support support 6813 May 28 19:51 routes.py
-rwxrwxr-x  1 support support 1759 May 28 19:51 run.sh
-rw-rw-r--  1 support support 1203 May 28 19:51 schemas.py
drwxrwxr-x  2 support support 4096 May 28 19:51 templates
-rw-rw-r--  1 support support 3078 May 28 19:51 ubuntu_guide.md
drwxrwxr-x  4 support support 4096 May 28 19:51 venv
```

У каталозі знаходяться файли Python-застосунку, шаблони, міграції Alembic, Dockerfile, Docker Compose та файл залежностей.

## Створення каталогу для деплою

Створюємо каталог для деплою:

```bash
support@maincopy:~$ mkdir -p /home/support/deploy/UMT-pythonweb-hw-11
```

Перевіряємо каталог:

```bash
support@maincopy:~$ ls -ld /home/support/deploy/UMT-pythonweb-hw-11
drwxrwxr-x 2 support support 4096 Jun 21 20:48 /home/support/deploy/UMT-pythonweb-hw-11
```

## Створення Bash-скрипта

Створюємо файл `/home/support/deploy.sh`:

```bash
support@maincopy:~$ cat > /home/support/deploy.sh <<'EOF'
#!/bin/bash

# Перевірка кількості аргументів та існування каталогів
if [ "$#" -ne 2 ] || [ ! -d "$1" ] || [ ! -d "$2" ]; then
    echo "Usage: ./deploy.sh <source_dir> <deploy_dir>"
    exit 1
fi

SOURCE_DIR=$(realpath "$1")
DEPLOY_DIR=$(realpath "$2")
LOCK_FILE="/tmp/deploy.lock"

# Захист від очищення каталогу з вихідними файлами
if [ "$SOURCE_DIR" = "$DEPLOY_DIR" ]; then
    echo "Deploy failed"
    exit 2
fi

# Захист від паралельного запуску
if [ -e "$LOCK_FILE" ]; then
    echo "Deploy already running"
    exit 1
fi

touch "$LOCK_FILE" || {
    echo "Deploy failed"
    exit 2
}

trap 'rm -f "$LOCK_FILE"' EXIT

# Очищення каталогу деплою
if ! find "$DEPLOY_DIR" -mindepth 1 -maxdepth 1 -exec rm -rf -- {} +; then
    echo "Deploy failed"
    exit 2
fi

# Копіювання нової версії застосунку
if ! cp -a "$SOURCE_DIR"/. "$DEPLOY_DIR"/; then
    echo "Deploy failed"
    exit 2
fi

echo "Deploy completed successfully"
exit 0
EOF
```

Скрипт перевіряє аргументи та каталоги, використовує lock-файл, очищає каталог деплою та копіює нову версію застосунку.

## Надання права на виконання

Робимо скрипт виконуваним:

```bash
support@maincopy:~$ chmod +x /home/support/deploy.sh
support@maincopy:~$ ls -l /home/support/deploy.sh
-rwxrwxr-x 1 support support 1107 Jun 21 20:53 /home/support/deploy.sh
```

## Перевірка аргументів

Перевіряємо запуск без аргументів:

```bash
support@maincopy:~$ /home/support/deploy.sh
echo "Exit code: $?"
Usage: ./deploy.sh <source_dir> <deploy_dir>
Exit code: 1
```

Перевіряємо запуск з одним аргументом:

```bash
support@maincopy:~$ /home/support/deploy.sh /home/support/UMT-pythonweb-hw-11
echo "Exit code: $?"
Usage: ./deploy.sh <source_dir> <deploy_dir>
Exit code: 1
```

Перевіряємо запуск із неіснуючим каталогом:

```bash
support@maincopy:~$ /home/support/deploy.sh /home/support/not-existing-app /home/support/deploy/UMT-pythonweb-hw-11
echo "Exit code: $?"
Usage: ./deploy.sh <source_dir> <deploy_dir>
Exit code: 1
```

У всіх випадках скрипт завершується з кодом `1`.

## Перевірка однакових каталогів

Перевіряємо захист від використання одного каталогу як джерела і каталогу деплою:

```bash
support@maincopy:~$ /home/support/deploy.sh /home/support/UMT-pythonweb-hw-11 /home/support/UMT-pythonweb-hw-11
echo "Exit code: $?"
Deploy failed
Exit code: 2
```

Скрипт зупиняє виконання, щоб не очистити вихідний каталог.

## Перевірка lock-файла

Створюємо lock-файл і запускаємо скрипт:

```bash
support@maincopy:~$ touch /tmp/deploy.lock

/home/support/deploy.sh \
    /home/support/UMT-pythonweb-hw-11 \
    /home/support/deploy/UMT-pythonweb-hw-11

echo "Exit code: $?"
rm -f /tmp/deploy.lock
Deploy already running
Exit code: 1
```

Скрипт визначає, що інший процес деплою вже запущений, і завершує роботу.

## Перевірка очищення каталогу

Створюємо у каталозі деплою два файли старої версії:

```bash
support@maincopy:~$ echo "Old application file" \
    > /home/support/deploy/UMT-pythonweb-hw-11/old_file.txt

echo "OLD_CONFIG=true" \
    > /home/support/deploy/UMT-pythonweb-hw-11/.old_config

ls -la /home/support/deploy/UMT-pythonweb-hw-11
total 16
drwxrwxr-x 2 support support 4096 Jun 21 20:57 .
drwxrwxr-x 3 support support 4096 Jun 21 20:48 ..
-rw-rw-r-- 1 support support   16 Jun 21 20:57 .old_config
-rw-rw-r-- 1 support support   21 Jun 21 20:57 old_file.txt
```

До запуску деплою каталог містить лише два тестові файли.

## Запуск деплою

Запускаємо деплой застосунку:

```bash
support@maincopy:~$ /home/support/deploy.sh \
    /home/support/UMT-pythonweb-hw-11 \
    /home/support/deploy/UMT-pythonweb-hw-11

echo "Exit code: $?"
Deploy completed successfully
Exit code: 0
```

Код `0` означає успішне завершення.

## Перевірка результату

Перевіряємо вміст каталогу після деплою:

```bash
support@maincopy:~$ ls -la /home/support/deploy/UMT-pythonweb-hw-11
total 112
drwxrwxr-x 6 support support 4096 Jun 21 20:50 .
drwxrwxr-x 3 support support 4096 Jun 21 20:48 ..
drwxrwxr-x 4 support support 4096 May 28 19:51 alembic
-rw-rw-r-- 1 support support 5008 May 28 19:51 alembic.ini
-rw-rw-r-- 1 support support 4162 May 28 19:51 auth_service.py
-rw-rw-r-- 1 support support  702 May 28 19:51 config.py
-rw-rw-r-- 1 support support 3957 May 28 19:51 crud.py
-rw-rw-r-- 1 support support  396 May 28 19:51 database.py
-rw-rw-r-- 1 support support  859 May 28 19:51 docker-compose.yml
-rw-rw-r-- 1 support support  204 May 28 19:51 Dockerfile
-rw-rw-r-- 1 support support 1182 May 28 19:51 email_service.py
-rw-rw-r-- 1 support support  668 May 28 19:51 .env
-rw-rw-r-- 1 support support  693 May 28 19:51 .env.example
-rw-rw-r-- 1 support support  314 May 28 19:51 .gitignore
-rw-rw-r-- 1 support support  870 May 28 19:51 main.py
-rw-rw-r-- 1 support support 1217 May 28 19:51 models.py
drwxrwxr-x 2 support support 4096 May 28 19:51 __pycache__
-rw-rw-r-- 1 support support 3839 May 28 19:51 README.md
-rw-rw-r-- 1 support support  191 May 28 19:51 requirements.txt
-rw-rw-r-- 1 support support 6813 May 28 19:51 routes.py
-rwxrwxr-x 1 support support 1759 May 28 19:51 run.sh
-rw-rw-r-- 1 support support 1203 May 28 19:51 schemas.py
drwxrwxr-x 2 support support 4096 May 28 19:51 templates
-rw-rw-r-- 1 support support 3078 May 28 19:51 ubuntu_guide.md
drwxrwxr-x 4 support support 4096 May 28 19:51 venv
```

Файли застосунку успішно скопійовано разом із прихованими файлами.

Перевіряємо наявність основних файлів:

```bash
support@maincopy:~$ test -f /home/support/deploy/UMT-pythonweb-hw-11/main.py \
    && echo "main.py copied"

test -f /home/support/deploy/UMT-pythonweb-hw-11/requirements.txt \
    && echo "requirements.txt copied"

test -f /home/support/deploy/UMT-pythonweb-hw-11/docker-compose.yml \
    && echo "docker-compose.yml copied"

test -d /home/support/deploy/UMT-pythonweb-hw-11/templates \
    && echo "templates directory copied"
main.py copied
requirements.txt copied
docker-compose.yml copied
templates directory copied
```

Перевіряємо, що тестові файли старої версії видалені:

```bash
support@maincopy:~$ test ! -e /home/support/deploy/UMT-pythonweb-hw-11/old_file.txt \
    && echo "Old file removed"

test ! -e /home/support/deploy/UMT-pythonweb-hw-11/.old_config \
    && echo "Old hidden file removed"
Old file removed
Old hidden file removed
```

Перевіряємо видалення lock-файла:

```bash
support@maincopy:~$ test ! -e /tmp/deploy.lock && echo "Lock file removed"
Lock file removed
```

## Висновок

Створено Bash-скрипт для деплою Python-застосунку. Скрипт перевіряє аргументи та каталоги, захищає процес від паралельного запуску, очищає каталог попередньої версії та копіює нову версію застосунку.

Під час перевірки скрипт правильно повернув:

- код `1` при неправильних аргументах або наявному lock-файлі;
- код `2` при спробі використати однакові каталоги;
- код `0` після успішного деплою.

Застосунок успішно скопійовано з каталогу:

```text
/home/support/UMT-pythonweb-hw-11
```

до каталогу:

```text
/home/support/deploy/UMT-pythonweb-hw-11
```
