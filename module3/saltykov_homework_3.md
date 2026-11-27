# Домашнє завдання до 3-го змістовного модуля: Веб-протоколи та прикладний рівень

* **Студент:** Салтиков Андрій Вікторович
* **Дисципліна:** Основи комп'ютерних мереж
* **Модуль:** 3. Веб-протоколи та прикладний рівень
* **Середовище виконання:** Сервер віртуалізації VMware ESXi (Linux VM)

---

## Завдання 1. Аналіз HTTP-заголовків та статус-кодів

### 1.1. Виконана команда та результат

```bash
support@maincopy:~$ curl -I http://github.com
HTTP/1.1 301 Moved Permanently
Content-Length: 0
Location: https://github.com/
```

---

### 1.2. Аналіз параметрів HTTP-відповіді

За результатами запиту до вебсервера `http://github.com` отримано такі параметри:

| Параметр | Значення | Пояснення |
| :--- | :--- | :--- |
| **Статус-код** | `301 Moved Permanently` | Код класу `3xx` (Redirection), який означає, що запитуваний ресурс було **назавжди переміщено** за новою URL-адресою, вказаною у заголовку `Location`. Клієнти (браузери) та пошукові індексатори кешують цей редирект і надалі автоматично звертаються за новою адресою. |
| **Заголовок `Location`** | `https://github.com/` | Вказує цільову адресу перенаправлення. Редирект відбувається з незахищеного протоколу `http://` (порт 80) на захищений `https://` (порт 443) з метою примусового використання шифрованого з'єднання (**HTTPS enforcement**) для захисту трафіку користувачів. |
| **Версія протоколу HTTP** | `HTTP/1.1` | Для початкової відповіді на незашифрований запит сервер використав текстовий протокол **`HTTP/1.1`** (при подальшому переході на `https://github.com/` з'єднання встановлюється вже за протоколом `HTTP/2`). |

---

## Завдання 2. Робота з методами HTTP

### 2.1. Виконана команда та результат

Для демонстрації створення ресурсу за допомогою методу `POST` було виконано запит до тестового REST API `dummyjson.com`:

```bash
support@maincopy:~$ curl -i -X POST https://dummyjson.com/posts/add \
  -H "Content-Type: application/json" \
  -d '{"title": "Test Homework", "userId": 1}'
HTTP/2 201 
date: Fri, 27 Nov 2026 10:10:24 GMT
content-type: application/json; charset=utf-8
content-length: 45
access-control-allow-credentials: true
etag: W/"2d-/CUhgJ/9IYFBy6aKpwkIIByjxSI"
strict-transport-security: max-age=15552000; includeSubDomains
vary: Origin, Accept-Encoding
x-content-type-options: nosniff
x-dns-prefetch-control: off
x-download-options: noopen
x-frame-options: SAMEORIGIN
x-ratelimit-limit: 100
x-ratelimit-remaining: 99
x-ratelimit-reset: 1795774231
x-xss-protection: 1; mode=block
cf-cache-status: DYNAMIC
report-to: {"group":"cf-nel","max_age":604800,"endpoints":[{"url":"https://a.nel.cloudflare.com/report/v4?s=0fdvzeey5zn3vUN2XDbo%2FfNqKFZ378OlQ5AIBIqxF1sHpIpr4xBlBVc3WNDod25H%2BESVXi%2FVQmSNI%2Fq7ofIeiNQZYgfXDVwdzvA5IawGgWnOpKUz1PFgTOhGgKVu1Oiu"}]}
nel: {"report_to":"cf-nel","success_fraction":0.0,"max_age":604800}
server: cloudflare
cf-ray: a61057c9ec0fb6b9-WAW

{"id":252,"title":"Test Homework","userId":1}
```

---

### 2.2. Аналіз результату та порівняння методів `POST` і `GET`

* **Статус-код успішного створення ресурсу:** **`201 Created`** (за протоколом `HTTP/2`). Цей статус-код підтверджує, що сервер успішно обробив запит і створив новий ресурс (у тілі відповіді повернуто JSON створеного об'єкта з присвоєним `"id": 252`).

#### Порівняння методів `GET` та `POST`:

| Критерій порівняння | Метод `GET` | Метод `POST` |
| :--- | :--- | :--- |
| **Основне призначення** | Отримання (читання) даних із сервера без зміни його стану. | Відправка даних на сервер для створення нового ресурсу або виконання дії, що змінює стан системи. |
| **Де передаються дані** | У **URL-рядку** як параметри запиту (*Query String* після символу `?`, наприклад: `?title=Test&userId=1`). Має обмеження на довжину URL. | Всередині **тіла HTTP-запиту (Request Body)** (у нашому прикладі — JSON-об'єкт із заголовком `Content-Type: application/json`). Дозволяє передавати великі обсяги даних та бінарні файли. |
| **Кешування** | **Кешується за замовчуванням** браузерами, проксі-серверами та CDN. Параметри зберігаються в історії браузера та логах серверів. | **Не кешується за замовчуванням** (у відповіді бачимо заголовок `cf-cache-status: DYNAMIC`), хіба що це явно вказано заголовками `Cache-Control` або `Expires`. |
| **Безпечність та ідемпотентність** | **Безпечний (Safe) та ідемпотентний (Idempotent):** багаторазове виконання одного `GET`-запиту повертає той самий ресурс і не має побічних ефектів на сервері. | **Небезпечний та неідемпотентний:** повторне надсилання одного й того самого `POST`-запиту призведе до повторного створення дублікатів ресурсу або повторної дії. |

---

## Завдання 3. Перевірка TLS-сертифіката

### 3.1. Виконана команда та результат

Для аналізу захищеного TLS-з’єднання було виконано підключення до сервера `github.com` на порт `443`:

```bash
support@maincopy:~$ openssl s_client -connect github.com:443 -servername github.com </dev/null
CONNECTED(00000003)
depth=2 C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication Root E46
verify return:1
depth=1 C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA DV E36
verify return:1
depth=0 CN = github.com
verify return:1
---
Certificate chain
 0 s:CN = github.com
   i:C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA DV E36
   a:PKEY: id-ecPublicKey, 256 (bit); sigalg: ecdsa-with-SHA256
   v:NotBefore: Oct 30 00:00:00 2026 GMT; NotAfter: Jan 27 23:59:59 2027 GMT
 1 s:C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA DV E36
   i:C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication Root E46
   a:PKEY: id-ecPublicKey, 256 (bit); sigalg: ecdsa-with-SHA384
   v:NotBefore: Mar 22 00:00:00 2021 GMT; NotAfter: Mar 21 23:59:59 2036 GMT
 2 s:C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication Root E46
   i:C = US, ST = New Jersey, L = Jersey City, O = The USERTRUST Network, CN = USERTrust ECC Certification Authority
   a:PKEY: id-ecPublicKey, 384 (bit); sigalg: ecdsa-with-SHA384
   v:NotBefore: Mar 22 00:00:00 2021 GMT; NotAfter: Jan 18 23:59:59 2038 GMT
---
subject=CN = github.com
issuer=C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA DV E36
---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: ECDSA
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 3094 bytes and written 376 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_128_GCM_SHA256
Server public key is 256 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
DONE
```

---

### 3.2. Аналіз параметрів TLS-з’єднання

| Параметр | Значення | Пояснення |
| :--- | :--- | :--- |
| **Хто видав сертифікат (`Issuer`)** | `C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA DV E36` | Сертифікат для домену `github.com` видано британським центром сертифікації **Sectigo Limited** (проміжний CA: *Sectigo Public Server Authentication CA DV E36*, кореневий CA: *USERTrust ECC Certification Authority*). |
| **Узгоджений протокол TLS** | **`TLSv1.3`** | Під час TLS Handshake між клієнтом та сервером було узгоджено найсучаснішу та найбезпечнішу версію протоколу — **TLS 1.3** (`Verification: OK`, `Verify return code: 0 (ok)`). |
| **Обраний шифр (`Cipher Suite`)** | **`TLS_AES_128_GCM_SHA256`** | Набір криптографічних алгоритмів включає:<br>• **AES_128_GCM** — симетричне блокове шифрування AES із ключем 128 біт у режимі Галуа/Лічильника (Galois/Counter Mode, AEAD — забезпечує одночасно конфіденційність та автентичність даних);<br>• **SHA256** — криптографічна хеш-функція для HKDF під час виведення ключів;<br>• Обмін тимчасовими ключами: **X25519** (ECDH на еліптичних кривих, Perfect Forward Secrecy), цифровий підпис сервера — **ECDSA** (256 біт). |

---

## Завдання 4. Аналіз безпеки Cookies

### 4.1. Виконана команда та результат

Проаналізуємо заголовки `Set-Cookie`, які встановлює сервер `https://github.com`:

```bash
support@maincopy:~$ curl -I https://github.com | grep -i "set-cookie"
set-cookie: _gh_sess=bX7B%2F8qL8sbAjt1k%2FoszYDSFS20ZvrJo%2FzRTgn3PODR08hP4r9EsPedIMeE5TsUyxqu%2F53pdWU3X7WkN1s7LsyYnM5Uaj6UY7Pw8A0AEWhTgHYmv8NM1wdyTgLZwScL87UgHq6odtBQwKlaF%2BIp3SRBaSzWhWOIs7n0FzT%2B%2Fty9DSI3EJWMT2j1un%2B3cYnKxrPh0yhsiQPTbt0BeOWG3TNof0yYI1mdimRuC%2FzNnQjkikygnHvWoAJnFEUbBUUXID7s9qbk6ZnBjnPvyOSw2Iw%3D%3D--qpfKMb89TQNUZCCR--XGdIikDSPsKfxFBQHWPY5w%3D%3D; path=/; HttpOnly; secure; SameSite=Lax
set-cookie: _octo=GH1.1.1433739125.1795774242; expires=Sat, 27 Nov 2027 10:10:42 GMT; domain=.github.com; path=/; secure; SameSite=Lax
set-cookie: logged_in=no; expires=Sat, 27 Nov 2027 10:10:42 GMT; domain=.github.com; path=/; HttpOnly; secure; SameSite=Lax
```

---

### 4.2. Аналіз параметрів та атрибутів безпеки Cookies

#### а) Виписані Cookie ID та атрибути:

| Назва (Cookie ID) | Призначення | Встановлені атрибути безпеки |
| :--- | :--- | :--- |
| **`_gh_sess`** | Зашифрований сесійний ідентифікатор користувача GitHub | `path=/`, **`HttpOnly`**, **`Secure`**, **`SameSite=Lax`** |
| **`logged_in`** | Прапорець стану авторизації (`no`), термін дії — 1 рік | `expires=Sat, 27 Nov 2027 10:10:42 GMT`, `domain=.github.com`, `path=/`, **`HttpOnly`**, **`Secure`**, **`SameSite=Lax`** |
| **`_octo`** | Допоміжний ідентифікатор клієнта/аналітики (`GH1.1.1433739125.1795774242`) | `expires=Sat, 27 Nov 2027 10:10:42 GMT`, `domain=.github.com`, `path=/`, **`Secure`**, **`SameSite=Lax`** *(відсутній `HttpOnly`, оскільки до неї потрібен доступ із клієнтського JS)* |

#### б) Від якої конкретної атаки захищає атрибут `HttpOnly`?
* Атрибут **`HttpOnly`** захищає сесійні кукі від крадіжки під час атаки **XSS (Cross-Site Scripting — міжсайтовий скриптинг)**.
* Коли для Cookie встановлено прапорець `HttpOnly`, браузер повністю блокує доступ до неї з боку клієнтських скриптів JavaScript (вона не відображається під час звернення до `document.cookie`). Навіть якщо зловмиснику вдасться виконати шкідливий JS-код у контексті сторінки жертви, він не зможе викрасти сесійний токен `_gh_sess` та перехопити акаунт користувача (**Session Hijacking**).

#### в) Пояснення ролі атрибута `Secure`:
* Атрибут **`Secure`** гарантує, що браузер передаватиме цю куку на сервер **виключно через зашифроване HTTPS-з’єднання (поверх TLS)**.
* Якщо користувач випадково перейде за незашифрованим посиланням `http://`, браузер не додасть куку з прапорцем `Secure` у заголовок `Cookie` запиту. Це захищає конфіденційні сесійні дані від перехоплення у відкритому вигляді в мережі під час атак **Man-in-the-Middle (MitM)** та прослуховування трафіку (**Network Sniffing**, наприклад, у відкритих мережах Wi-Fi).
* *(Додатково атрибут **`SameSite=Lax`** захищає від атак **CSRF — Cross-Site Request Forgery**, забороняючи автоматичне надсилання кукі під час фонових міжсайтових POST-запитів із зовнішніх ресурсів).*

---

## Завдання 5. Архітектурне моделювання (Кейс)

### 5.1. Сценарій а: Оновлення графіків цін у браузері користувача в реальному часі

* **Обраний протокол:** **WebSockets (`wss://`)**
* **Обґрунтування:**
  * На біржі ціни змінюються щосекунди. Звичайні HTTP-запити (polling) створюють зайве навантаження, бо клієнту доводиться постійно відкривати нові запити і ганяти великі заголовки.
  * WebSocket встановлює одне постійне двостороннє (full-duplex) з'єднання між браузером і сервером поверх TCP/TLS.
  * Коли ціна акції змінюється, сервер сам миттєво пушить нові дані в браузер із мінімальною затримкою та без зайвого трафіку.

---

### 5.2. Сценарій б: Взаємодія між внутрішніми мікросервісами (білінг `<->` облік)

* **Обраний протокол:** **gRPC (поверх HTTP/2)**
* **Обґрунтування:**
  * **Швидкість та мінімальна затримка:** замість текстового JSON використовується бінарний формат **Protocol Buffers (Protobuf)**. Дані важать у рази менше і парсяться процесором набагато швидше, що важливо для швидких біржових операцій.
  * **Робота поверх HTTP/2:** підтримує мультиплексування — білінг і сервіс обліку можуть слати багато паралельних запитів в одному TCP-з'єднанні без черг і затримок.
  * **Сувора типізація:** контракт описується у `.proto` файлі, тому сервіси гарантовано обмінюються правильними типами даних (що критично при роботі з грошима та балансами акцій).

