# Домашнє завдання до 1-го змістовного модуля: Основи комп'ютерних мереж

* **Студент:** Салтиков Андрій Вікторович
* **Дисципліна:** Основи комп'ютерних мереж
* **Модуль:** 1. Основи комп'ютерних мереж
* **Середовище виконання:** Сервер віртуалізації VMware ESXi (Linux VM)

---

## Завдання 1. Дослідження мережі

### 1.1. Виконана команда та результат

```bash
support@maincopy:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:fc:93:79 brd ff:ff:ff:ff:ff:ff
    altname enp3s0
    inet 192.168.8.34/26 metric 100 brd 192.168.8.63 scope global dynamic ens160
       valid_lft 481215sec preferred_lft 481215sec
    inet6 fe80::20c:29ff:fefc:9379/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 66:77:a1:05:14:89 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::6477:a1ff:fe05:1489/64 scope link
       valid_lft forever preferred_lft forever
4: vethf119132@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 82:58:35:95:11:20 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::8058:35ff:fe95:1120/64 scope link
       valid_lft forever preferred_lft forever
7: br-b48107511831: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 0e:b0:5a:e3:37:cc brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-b48107511831
       valid_lft forever preferred_lft forever
    inet6 fe80::cb0:5aff:fee3:37cc/64 scope link
       valid_lft forever preferred_lft forever
8: vethfc6825b@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-b48107511831 state UP group default
    link/ether 2e:27:e9:ef:59:c7 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::2c27:e9ff:feef:59c7/64 scope link
       valid_lft forever preferred_lft forever
9: veth1eda997@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-b48107511831 state UP group default
    link/ether a6:25:22:2f:23:7f brd ff:ff:ff:ff:ff:ff link-netnsid 2
    inet6 fe80::a425:22ff:fe2f:237f/64 scope link
       valid_lft forever preferred_lft forever
14: veth1f8952b@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-b48107511831 state UP group default
    link/ether 5e:0f:ad:3d:01:2d brd ff:ff:ff:ff:ff:ff link-netnsid 3
    inet6 fe80::5c0f:adff:fe3d:12d/64 scope link
       valid_lft forever preferred_lft forever
```

---

### 1.2. Аналіз параметрів мережевого інтерфейсу

Для основного робочого мережевого адаптера **`ens160`**:

| Параметр | Значення | Опис та рівень моделі OSI |
| :--- | :--- | :--- |
| **Інтерфейс** | `ens160` | Основний фізичний/віртуальний мережевий інтерфейс системи (VMware VMXNET3 Virtual Network Adapter). На системі також присутні: `lo` (loopback-інтерфейс), `docker0` та `br-b48107511831` (віртуальні мережеві мости Docker), а також `veth*` (віртуальні Ethernet-пари для ізоляції контейнерів). |
| **Тип підключення** | `Ethernet` (`link/ether`) | Дротове мережеве підключення стандарту IEEE 802.3, організоване через віртуальний комутатор VMware ESXi (vSwitch). |
| **MAC-адреса** | `00:0c:29:fc:93:79` | Фізична апаратна адреса адаптера. Належить до **Канального рівня (Data Link Layer, L2)** моделі OSI. Префікс OUI `00:0c:29` офіційно закріплений за компанією **VMware, Inc.** (автоматично згенерована адреса віртуальної машини ESXi). |
| **IP-адреса** | `192.168.8.34` (IPv4)<br>`fe80::20c:29ff:fefc:9379` (IPv6 Link-Local) | Логічна адреса хоста в локальній мережі. Належить до **Мережевого рівня (Network Layer, L3)** моделі OSI. Адреса належить до приватного діапазону класу C (RFC 1918). |
| **Маска підмережі** | `/26`<br>(десяткова: `255.255.255.192`) | Визначає межу між ідентифікатором мережі та хостовою частиною. Належить до **Мережевого рівня (Network Layer, L3)** моделі OSI. Префікс `/26` означає, що перші 26 біт відведено під адресу мережі, а 6 біт — під адресацію хостів. |
| **Шлюз (Gateway)** | `192.168.8.1` | Маршрутизатор за замовчуванням (Default Gateway), через який здійснюється вихід з підмережі (визначено за допомогою команди `ip r`). **Мережевий рівень (L3)**. |
| **Broadcast адреса** | `192.168.8.63` | Широкомовна адреса підмережі для надсилання пакетів усім вузлам сегмента (`brd 192.168.8.63`). **Мережевий рівень (L3)**. |
| **Кількість хостів (Hosts)** | **62 доступних хости** | **Розрахунок:**<br>1. Кількість біт під хости: $32 - 26 = 6$ біт.<br>2. Загальна кількість адрес у підмережі: $2^6 = 64$.<br>3. Кількість адрес, доступних для хостів: $2^6 - 2 = 62$ (віднімається адреса мережі та широкомовна адреса).<br>4. Адреса мережі: `192.168.8.0/26`.<br>5. Діапазон адрес хостів: від `192.168.8.1` до `192.168.8.62`. |

---

## Завдання 2. Дослідження маршрутизації

### 2.1. Виконана команда та результат

```bash
support@maincopy:~$ ip r
default via 192.168.8.1 dev ens160 proto dhcp src 192.168.8.34 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1
172.18.0.0/16 dev br-b48107511831 proto kernel scope link src 172.18.0.1
192.168.8.0/26 dev ens160 proto kernel scope link src 192.168.8.34 metric 100
192.168.8.1 dev ens160 proto dhcp scope link src 192.168.8.34 metric 100
192.168.8.7 dev ens160 proto dhcp scope link src 192.168.8.34 metric 100
192.168.8.8 dev ens160 proto dhcp scope link src 192.168.8.34 metric 100
```

---

### 2.2. Аналіз таблиці маршрутизації

Таблиця містить розвинену конфігурацію маршрутів:

1. **Маршрут за замовчуванням (Default Gateway):**
   * `default via 192.168.8.1 dev ens160 proto dhcp src 192.168.8.34 metric 100` — увесь нелокальний трафік надсилається через маршрутизатор `192.168.8.1` з інтерфейсу `ens160`. Метрика `100` визначає пріоритет маршруту.
2. **Локальний маршрут підмережі:**
   * `192.168.8.0/26 dev ens160 proto kernel scope link src 192.168.8.34 metric 100` — пряма комунікація з пристроями локального сегмента без залучення шлюзу.
3. **Прямі маршрути до службових серверів інфраструктури:**
   * `192.168.8.1`, `192.168.8.7`, `192.168.8.8 dev ens160 proto dhcp` — хостові маршрути (/32), отримані по DHCP, які зазвичай вказують на шлюз, а також корпоративні DNS-сервери або контролери домену.
4. **Маршрути підсистеми Docker-контейнеризації:**
   * `172.17.0.0/16 dev docker0` та `172.18.0.0/16 dev br-b48107511831` — маршрути для ізольованих віртуальних мереж Docker контейнерів.

### 2.3. Протокол, через який отримано IP-адресу та маршрути

* **`proto dhcp`** — IP-адреса хоста `192.168.8.34`, шлюз `192.168.8.1` та допоміжні маршрути були динамічно отримані за протоколом **DHCP (Dynamic Host Configuration Protocol)** від мережевого DHCP-сервера інфраструктури (час оренди у виводі `ip a` становить `valid_lft 481215sec`).
* **`proto kernel`** — безпосередньо приєднані локальні маршрути підмереж (`scope link`), які ядро Linux автоматично додає під час підняття інтерфейсів.

---

## Завдання 3. Знаходження публічної IP-адреси

### 3.1. Виконана команда та результат

```bash
support@maincopy:~$ curl ifconfig.io
198.51.100.97
```

> **Примітка щодо кібербезпеки (OpSec / RFC 5737):**
> З міркувань дотримання політики конфіденційності та інформаційної безпеки корпоративної мережі підприємства, оригінальну публічну IP-адресу компанії у цьому публічному звіті анонімізовано за стандартом **RFC 5737 (IPv4 Address Blocks Reserved for Documentation — TEST-NET-2)** на адресу **`198.51.100.97`**.

* **Публічна IP-адреса (анонімізована):** `198.51.100.97`

---

### 3.2. Порівняння та аналіз локальної та публічної адрес

* **Локальна IP-адреса (Private IP):** `192.168.8.34`
* **Публічна IP-адреса (Public IP):** `198.51.100.97`

#### 1. Чи вони однакові?
**Ні, вони абсолютно різні.** Внутрішня адреса машини — `192.168.8.34`, а зовнішня публічна адреса шлюзу в мережі Інтернет — `198.51.100.97`.

#### 2. Чому вони різні та яку роль відіграє маршрутизатор (роутер / фаєрвол)?

* **Приватний діапазон адрес (RFC 1918):**
  Адреса `192.168.8.34` належить до діапазону приватних адрес класу C (`192.168.0.0/16`). Такі адреси використовуються виключно всередині локальних корпоративних чи домашніх мереж. Вони не є глобально унікальними та не маршрутизуються вузлами глобальної мережі Інтернет.
* **Публічна адреса:**
  Адреса `198.51.100.97` є глобально унікальною адресою, зареєстрованою в базах RIPE NCC та виділеною провайдером для виходу підприємства в інтернет.
* **Роль роутера / міжмережевого екрана та механізм NAT (Network Address Translation):**
  * Корпоративний маршрутизатор (або фаєрвол на межі мережі) виконує роль шлюзу між внутрішньою інфраструктурою та зовнішнім світом.
  * Використовуючи технологію **SNAT / PAT (Source NAT / Port Address Translation / Masquerading)**, маршрутизатор замінює приватну IP-адресу джерела (`192.168.8.34`) на свою публічну зовнішню адресу під час надсилання вихідних пакетів в Інтернет.
  * Роутер зберігає у пам'яті таблицю стану з'єднань (Stateful Connection Tracking Table), зіставляючи локальну IP-адресу і порт із зовнішнім портом. Коли віддалений вебсервер відповідає, маршрутизатор транслює адресу призначення назад у `192.168.8.34` і доставляє пакет потрібній віртуальній машині.
  * Це дозволяє тисячам внутрішніх серверів і комп'ютерів безпечно ділити одну або кілька публічних IP-адрес, захищаючи внутрішні вузли від прямого несанкціонованого доступу з Інтернету.
