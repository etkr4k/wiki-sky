---
title: Network
description: 
published: true
date: 2022-08-31T13:37:17.183Z
tags: 
editor: markdown
dateCreated: 2022-07-12T13:36:59.734Z
---

https://xn----7sba7aachdbqfnhtigrl.xn--j1amh/kak-nastroit-router-mikrotik-bazovaya-konfiguracziya/
# ZeroTier
Using the graphical ZeroTier installer won’t work in an RDP session.  
There is a command-line workaround, open a PowerShell with Administrative Privileges and paste:
`msiexec /i "C:\Path\To\ZeroTier One.msi"`
Try the msiexec’s silent install options too

# Reset Configuration
### Reset
Настройка находится в System → Reset Configuration

> Установить переключатели No Default Configuration и Do Not Backup;
> Нажать кнопку Reset Configuration.
{.is-info}

`/system reset-configuration no-defaults=yes skip-backup=yes`

### Error
> ERROR: router does not support secure connection, please enable legacy mode if you want to connect anyway
> 
> Решение: необходимо активировать режим Legacy Mode.
{.is-danger}

> ERROR: wrong username or passwords
> 
> Решение 1: Недопустимые учётные данные(неверное имя пользователя или пароль). За доступом нужно обратиться к администратору устройства или сделать сброс к заводским настройкам.
> 
> Решение 2: Обновить версию Winbox.
{.is-danger}

> ERROR: could not connect to MikroTik-Ip-Address
> 
> Решение: Проблема связана с доступом, частые причины:
> 
> Подключение закрыто через Firewall;
> Изменён порт управления через Winbox с 8291 на другой;
> Подключение происходит через WAN порт, интернет провайдер которого блокирует подобные соединения;
{.is-danger}

### Create user/set password
Настройка находится в System → Users

> Нажать + и добавить новую учётную запись администратора;
> Заполнить параметры: Name, Group, Password;
> Открыть учётную запись admin и деактивировать кнопкой Disable.
{.is-info}

`/user add name="admin-2" password="PASSWORD" group=full`

`/user set [find name="admin"] disable="yes"`

# Upgrade OS
### Upgrade RouterOS Auto
Настройка находится в System → Packages

> Нажать кнопку Check For Updates;
> Выбрать Channel = long term;
> Загрузить и установить прошивку на MikroTik кнопкой Download&Install.
{.is-info}

### Upgrade RouterOS Manual
Скачать нужную прошивку с сайта mikrotik.com
Загрузить ее на роутер в раздел Files
Презагрузить роутер.

### Upgrade Current Firmware
Настройка находится тут System → Routerboard → Upgrade
System → Reboot

# LAN Settings
### Bridge
Создаем Bridge
Настройка находится в основном меню Bridge → Bridge

> Нажать + и добавить новый Bridge;
> Присвоить Name для выбранного Bridge;
> Нажать кнопку Apply и скопировать значение MAC Address в Admin MAC Address;
{.is-info}

`/interface bridge add name=bridge-1`

Добавляем порты в bridge
Настройка находится в основном меню Bridge → Ports

> Нажать + и добавить новый Port;
> Выбрать соответствующие значение в параметрах: Interface, Bridge;
> Повторить аналогичные действия для всех интерфейсов, которые определены как LAN.
{.is-info}

`/interface bridge port add bridge=bridge-1 hw=yes interface=ether2`
`/interface bridge port add bridge=bridge-1 hw=yes interface=ether3`
`/interface bridge port add bridge=bridge-1 hw=yes interface=ether4`
`/interface bridge port add bridge=bridge-1 hw=yes interface=ether5`

`/interface bridge port add bridge=bridge-1 interface=wlan1`
`/interface bridge port add bridge=bridge-1 interface=wlan2`

### IP Settings
Настройка находится в IP → Addresses

> Нажать + и добавить новый IP адрес;
> Заполнить параметры: Address, Interface.
{.is-info}

`/ip address add address=192.168.0.1/24 interface=bridge-1 network=192.168.0.0`

### DHCP Settings
Создаем Pool
Настройка находится в IP→Pool

> Нажать + и добавить новый IP Pool;
> Заполнить параметры: Name, Addresses.
{.is-info}

`/ip pool add name=pool-1 ranges=192.168.0.2-192.168.0.254`

Задаем настройки для клиентов
Настройка находится в IP → DHCP Server → Networks

> Нажать + и добавить новую DHCP сеть;
> Заполнить параметры: Address, Gateway, Netmask, DNS Server.
{.is-info}

- Netmask = 24 – это эквивалент привычному значению 255.255.255.0;
- Gateway -шлюз по умолчанию(роутер MikroTik);
- DNS Servers – DNS сервер, который будет выдан клиенту. В данном случае это также роутер MikroTik.

`/ip dhcp-server network add address=192.168.0.0/24 dns-server=192.168.0.1 gateway=192.168.0.1 netmask=24`

Общие настройки DHCP
Настройка находится в IP → DHCP Server → DHCP


> Нажать + и добавить новый DHCP сервер;
> Заполнить параметры: Interface, Address Pool.
{.is-info}

`/ip dhcp-server add address-pool=pool-1 disabled=no interface=ether-1 lease-time=1w name=server-1`

### DNS Settings
Настройка находится в IP → DNS

> Задать внешние DNS сервера в параметре Servers. Это может быть DNS сервера Google: 8.8.8.8 и 8.8.4.4 или Cloudflare: 1.1.1.1 и 1.0.0.1;
> Активировать параметр Allow Remote Requests. Это разрешит внешним запросам обращаться к роутеру MikroTik как к DNS серверу;
> Обратить внимание на Cache Size. В больших сетях(от 100 узлов) его стоит увеличить в 2 или 3 раза. По умолчанию его значение = 2048Кб.
{.is-info}

`/ip dns
set allow-remote-requests=yes servers=8.8.8.8,8.8.4.4`

# WAN Settings
### Static WAN Settings
Настройка находится в IP → Addresses

> Нажать + и добавить новый IP адрес;
> Заполнить параметры: Address, Interface.
{.is-info}

`/ip address add address=81.21.12.15/27 interface=ether1 network=81.21.12.0`

### Static WAN Route
Настройка находится в IP → Routes

> Нажать + и добавить новый статический маршрут в MikoTik;
> Заполнить параметры: Gateway.
{.is-info}

- Dst. Address = 0.0.0.0/0 – типичное обозначение для любого трафика. Таким значением в MikroTik необходимо определять интернет трафик;
- Gateway – это шлюз по умолчанию со стороны интернет провайдера, который подключен к роутеру MikroTik.

`/ip route distance=1 gateway=81.21.12.1`

# NAT Settings
Настройка находится в IP → Firewall → NAT

> Нажать + и добавить новое правило NAT;
> Установить Chain = srcnat;
> Out Interface = интерфейс с интернетом;
> Action = Masquerade.
{.is-info}

`/ip firewall nat add action=masquerade chain=srcnat out-interface=ether1`

# Wi-Fi Settings
### Set Wi-Fi profile
Настройка находится в Wireless → Security Profiles

> Открыть профиль default для установки пароля WiFi ;
> Установить Mode = dynamic keys;
> Authentication Types = WPA2 PSK;
> Chiphers = aes com;
> WPA2 Pre-Shared Key – пароль для WiFi.
{.is-info}

`/interface wireless security-profiles
set [ find default=yes ] authentication-types=wpa2-psk eap-methods="" \
group-key-update=1h mode=dynamic-keys supplicant-identity=MikroTik \
wpa2-pre-shared-key=12345678`

### Set Wi-Fi AP
Настройки находятся Wireless → WiFi Interfaces

> Открыть WiFi интерфейс wlan1;
> Установить режим работы точки доступа Mode = ap bridge;
> Поддерживаемые стандарты WiFi Band = 2Ghz-B/G/N;
> Ширину канала Channel Width =20/40Mhz Ce;
> Частоту WiFi Frequency = auto;
> Имя WiFi сети SSID = MikroTik;
> Пароль для WiFi Security Profile = default.
{.is-info}

`/interface wireless
set [ find default-name=wlan1 ] band=2ghz-b/g/n channel-width=20/40mhz-Ce \
disabled=no distance=indoors frequency=auto mode=ap-bridge ssid=Mikrotik \
wireless-protocol=802.11`

