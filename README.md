# *demoexam 2025*
![image](https://github.com/Demomen-saveTF2/SA1-22demo2025/blob/main/topology.png)
## Задание 1

### Произведите базовую настройку устройств

- Настройте имена устройств согласно топологии. Используйте полное доменное имя

- На всех устройствах необходимо сконфигурировать IPv4

- IP-адрес должен быть из приватного диапазона, в случае, если сеть локальная, согласно RFC1918

- Локальная сеть в сторону HQ-SRV(VLAN100) должна вмещать не более 64 адресов

- Локальная сеть в сторону HQ-CLI(VLAN200) должна вмещать не более 16 адресов

- Локальная сеть в сторону BR-SRV должна вмещать не более 32 адресов

- Локальная сеть для управления(VLAN999) должна вмещать не более 8 адресов

- Сведения об адресах занесите в отчёт, в качестве примера используйте Таблицу 3

<br/>

<details>
<summary>Решение</summary>
<br/>

**Полное доменное имя можно посмотреть в таблице для [Задания 9](https://github.com/Demomen-saveTF2/SA1-22demo2025/blob/main/README.md#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-9)**

<br/>

#### Настройка имен устройств на ALT Linux
```yml
hostnamectl set-hostname <полное_доменное_имя>; exec bash
```
> `exec bash` - обновление оболочки, то есть имя настраивается без необходимости в перезагрузке устройства

<br/>

#### Настройка имен устройств на EcoRouter

Переходим в режим конфигурации и прописываем следующее:
```yml
en
conf
hostname <полное_доменное_имя>
end
wr mem
```

<br/>

> При расчете подсетей не забывайте считать количество узлов по формуле n-2 (где n - количество адресов). Если сеть должна вмещать не более 64 адресов, это значит что в сети может быть 62 устройства (один адрес уходит на подсеть, а другой широковещательный). Если считаете при помощи онлайн калькулятора вводите не 64, а 62, 32 &mdash; 30, 16 &mdash; 14, 8 &mdash; 6 

![image](https://github.com/Demomen-saveTF2/SA1-22demo2025/blob/main/маски_подсети.jpg)

<p align="center"><strong>Таблица подсетей</strong></p>
<table align="center">
  <tr>
    <td align="center">Сеть</td>
    <td align="center">Адрес подсети</td>
    <td align="center">Пул-адресов</td>
  </tr>
  <tr>
    <td align="center">SRV-Net (VLAN 100)</td>
    <td align="center">192.168.100.0/26</td>
    <td align="center">192.168.100.1 - 192.168.100.62</td>
  </tr>
  <tr>
    <td align="center">CLI-Net (VLAN 200)</td>
    <td align="center">192.168.200.0/28</td>
    <td align="center">192.168.200.1 - 192.168.200.14</td>
  </tr>
  <tr>
    <td align="center">BR-Net</td>
    <td align="center">192.168.0.0/27</td>
    <td align="center">192.168.0.1 - 192.168.0.30</td>
  </tr>
  <tr>
    <td align="center">MGMT (VLAN 999)</td>
    <td align="center">192.168.99.0/29</td>
    <td align="center">192.168.99.1 - 192.168.99.6</td>
  </tr>
  <tr>
    <td align="center">ISP-HQ</td>
    <td align="center">172.16.4.0/28</td>
    <td align="center">172.16.4.1 - 172.16.4.14</td>
  </tr>
  <tr>
    <td align="center">ISP-BR</td>
    <td align="center">172.16.5.0/28</td>
    <td align="center">172.16.5.1 - 172.16.5.14</td>
  </tr>
</table>


> При создании интерфейсов на EcoRouter, называйте их соседними устройствами, так вам будет легче определить, что это за интерфейс
<br/>
<p align="center"><strong>Таблица адресации</strong></p>
<table align="center">
  <tr>
    <td align="center">Имя устройства</td>
    <td align="center">Интерфейс</td>
    <td align="center">IPv4/IPv6</td>
    <td align="center" >Маска/Префикс</td>
    <td align="center">Шлюз</td>
  </tr>
  <tr>
    <td align="center" rowspan="3">ISP</td>
    <td align="center">ens18</td>
    <td align="center">10.12.28.5 (DHCP)</td>
    <td align="center">/24</td>
    <td align="center">10.12.28.254</td>
  </tr>
  <tr>
    <td align="center">ens19</td>
    <td align="center">172.16.5.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">ens20</td>
    <td align="center">172.16.4.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center" rowspan="4">HQ-RTR</td>
    <td align="center">HQ-RTR-ISP</td>
    <td align="center">172.16.4.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.4.1</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR-SRV</td>
    <td align="center">192.168.100.1</td>
    <td align="center">/26</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">HQ-RTR-CLI</td>
    <td align="center">192.168.200.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">MGMT-VLAN</td>
    <td align="center">192.168.99.1</td>
    <td align="center">/28</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center" rowspan="2">BR-RTR</td>
    <td align="center">BR-RTR-ISP</td>
    <td align="center">172.16.5.2</td>
    <td align="center">/28</td>
    <td align="center">172.16.5.1</td>
  </tr>
  <tr>
    <td align="center">BR-RTR-SRV</td>
    <td align="center">192.168.0.1</td>
    <td align="center">/27</td>
    <td align="center"></td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">ens33</td>
    <td align="center">192.168.100.2</td>
    <td align="center">/26</td>
    <td align="center">192.168.100.1</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">ens33</td>
    <td align="center">192.168.0.2</td>
    <td align="center">/27</td>
    <td align="center">192.168.0.1</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">ens33</td>
    <td align="center">192.168.200.2</td>
    <td align="center">/28</td>
    <td align="center">192.168.200.1</td>
  </tr>
</table>


> Адресация для **ISP** взята из следующего задания

<br/>

#### Наcтройка IP-адресации на **HQ-SRV**, **BR-SRV**, **HQ-CLI** (настройка IP-адресации на **ISP** проводится в [следующем задании]())

Приводим файлы **`options`**, **`ipv4address`**, **`ipv4route`** в директории **`/etc/net/ifaces/<имя интерфейса>/`** к следующему виду (в примере **HQ-SRV**):
```yml
DISABLED=no
TYPE=eth
BOOTPROTO=static
CONFIG_IPV4=yes
```

```yml
echo "BOOTPROTO=static" > /etc/net/ifaces/ens18/options
echo "TYPE=eth" >> /etc/net/ifaces/ens18/options
echo "CONFIG_WIRELESS=no" >> /etc/net/ifaces/ens18/options
echo "SYSTEMD_BOOTPROTO=static" >> /etc/net/ifaces/ens18/options
echo "CONFIG_IPV4=yes" >> /etc/net/ifaces/ens18/options
echo "DISABLED=no" >> /etc/net/ifaces/ens18/options
echo "NM_CONTROLLED=no" >> /etc/net/ifaces/ens18/options
echo "SYSTEMD_CONTROLLED=no" >> /etc/net/ifaces/ens18/options
```
> **`options`**


```yml
echo "192.168.100.62/26" > /etc/net/ifaces/ens18/ipv4address
```

```yml
echo "<ip-адрес/маска>" > /etc/net/ifaces/ens18/ipv4address
```
> **`ipv4address`**


```yml
echo "default via 192.168.100.1" > /etc/net/ifaces/ens18/ipv4route
```

```yml
echo "default via <адрес шлюза>" > /etc/net/ifaces/ens18/ipv4route
```
> **`ipv4route`**

<br/>

#### Настройка IP-адресации на EcoRouter

**Адресация на HQ-RTR (с разделением на VLAN)**
```yml
en
conf
int HQ-RTR-ISP
ip address 172.16.4.2/28
port te0
service-inst 0
enc untagged
conn ip int HQ-RTR-ISP
exit

int HQ-RTR-SRV
ip address 192.168.100.1/26
port te1
service-inst 100
enc dot1q 100
rewrite pop 1
conn ip int HQ-RTR-SRV
exit

int HQ-RTR-CLI
ip address 192.168.200.1/28
port te1
service-inst 200
enc dot1q 200
rewrite pop 1
conn ip int HQ-RTR-CLI
exit

int VLAN999
ip address 192.168.99.1/29
port te1
service-inst 999
enc dot1q 999
rewrite pop 1
conn ip int VLAN999
end
wr mem
```
<br/>

**Адресация на BR-RTR (без разделения на VLAN)**
```yml
en
conf
int BR-RTR-ISP
ip address 172.16.5.2/28
port te0
service-inst 0
enc untagged
conn ip int BR-RTR-ISP
exit

int BR-RTR-SRV
ip address 192.168.0.1/27
port te1
service-inst 0
enc untagged
conn ip int  BR-RTR-SRV
end
wr mem
```
<br/>

#### Добавление маршрута по умолчанию в EcoRouter
Заходим в конфиг и прописываем следующее:
```yml
ip route 0.0.0.0 0.0.0.0 *адрес шлюза*
```
HQ-RTR
```yml
conf
ip route 0.0.0.0 0.0.0.0 172.16.4.1
ex
wr mem
```

BR-RTR
```yml
conf
ip route 0.0.0.0 0.0.0.0 172.16.5.1
ex
wr mem
```
</details>

<br/>

## Задание 2

### Настройка ISP

- Настройте адресацию на интерфейсах:

  - Интерфейс, подключенный к магистральному провайдеру, получает адрес по DHCP

  - Настройте маршруты по умолчанию там, где это необходимо

  - Интерфейс, к которому подключен HQ-RTR, подключен к сети 172.16.4.0/28

  - Интерфейс, к которому подключен BR-RTR, подключен к сети 172.16.5.0/28

  - На ISP настройте динамическую сетевую трансляцию в сторону HQ-RTR и BR-RTR для доступа к сети Интернет

<br/>

<details>
<summary>Решение</summary>
<br/>

#### Настройка внешнего интерфейса, IP-адрес получает по DHCP

Файл **`options`** (в директории интерфейса) приводим к следующему виду:
```yml
echo "BOOTPROTO=dhcp" > /etc/net/ifaces/ens18/options
echo "TYPE=eth" >> /etc/net/ifaces/ens18/options
echo "DISABLED=no" >> /etc/net/ifaces/ens18/options
echo "CONFIG_IPV4=yes" >> /etc/net/ifaces/ens18/options
systemctl restart network
```
<br/>

#### Настройка интерфейсов, смотрящих в сторону HQ-RTR и BR-RTR происходит в [Задании 1]()
```yml
mkdir /etc/net/ifaces/ens19
echo "172.16.4.1/28" > /etc/net/ifaces/ens19/ipv4address
echo "BOOTPROTO=static" > /etc/net/ifaces/ens19/options
echo "TYPE=eth" >> /etc/net/ifaces/ens19/options
echo "DISABLED=no" >> /etc/net/ifaces/ens19/options
echo "CONFIG_IPV4=yes" >> /etc/net/ifaces/ens19/options
mkdir /etc/net/ifaces/ens20
echo "172.16.5.1/28" > /etc/net/ifaces/ens20/ipv4address
echo "BOOTPROTO=static" > /etc/net/ifaces/ens20/options
echo "TYPE=eth" >> /etc/net/ifaces/ens20/options
echo "DISABLED=no" >> /etc/net/ifaces/ens20/options
echo "CONFIG_IPV4=yes" >> /etc/net/ifaces/ens20/options
systemctl restart network
```
<br/>


##### Включение маршрутизации и настройка NAT на ISP

Скачиваем iptables, добавляем правила **`iptables`** на ISP, сохраняем их и включаем в автозагрузку. Перезагружаем:
> -o ens33 &mdash; указываем **выходной** интерфейс
```yml
apt-get update
apt-get install iptables -y
systemctl enable --now iptables
iptables -t nat -A POSTROUTING -o ens18 -j MASQUERADE -s 172.16.4.0/28
iptables -t nat -A POSTROUTING -o ens18 -j MASQUERADE -s 172.16.5.0/28
iptables-save -f /etc/sysconfig/iptables
```
#### Включение маршрутизации
В файле **`/etc/net/sysctl.conf`**изменяем строку (0 меняем на 1):
```yml
apt-get update
apt-get install -y nano
nano /etc/net/sysctl.conf
```
```yml
net.ipv4.ip_forward = 1
```
Перезагружаем iptables:
```yml
systemctl restart iptables
sysctl -p /etc/net/sysctl.conf
```
<br/>
</details>

## Задание 3

### Создание локальных учетных записей

- Создайте пользователя sshuser на серверах HQ-SRV и BR-SRV

  - Пароль пользователя sshuser с паролем P@ssw0rd

  - Идентификатор пользователя 1010

  - Пользователь sshuser должен иметь возможность запускать sudo без дополнительной аутентификации.

- Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR-RTR

  - Пароль пользователя net_admin с паролем P@$$word

  - При настройке на EcoRouter пользователь net_admin должен обладать максимальными привилегиями

  - При настройке ОС на базе Linux, запускать sudo без дополнительной аутентификации

<br/>

<details>
<summary>Решение</summary>
<br/>

#### Создание пользователя sshuser на HQ-SRV и BR-SRV

Создаем пользователя с идентификатором 1010 (-u) и принадлежностью к группе wheel (-G), добавляем право в файл **`/etc/sudoers`** и задаем ему пароль:
```yml
useradd -u 1010 -G wheel sshuser
echo "sshuser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
passwd sshuser
```

<br/>

#### Создание пользователя net_admin на HQ-RTR и BR-RTR

Создаем пользователя и задаем ему роль:
```yml
conf
username net_admin
password P@$$word
role admin
end
wr mem
```

</details>

<br/>

## Задание 4

### Настройка безопасного удаленного доступа на серверах HQ-SRV и BR-SRV

- Для подключения используйте порт 2024
- Разрешите подключения только пользователю sshuser
- Ограничьте количество попыток входа до двух
- Настройте баннер «Authorized access only»

<br/>

<details>
<summary>Решение</summary>
<br/>

С помощью echo перенаправляем строки в файл **`/etc/openssh/sshd_config`** и создаем файл **`bannermotd`**. Перезагружаем демон sshd:
```yml
echo "Port 2024" >> /etc/openssh/sshd_config
echo "MaxAuthTries 2" >> /etc/openssh/sshd_config
echo "PasswordAuthentication yes" >> /etc/openssh/sshd_config
echo "AllowUsers    sshuser" >> /etc/openssh/sshd_config
echo "Banner /etc/openssh/bannermotd" >> /etc/openssh/sshd_config
echo "Authorized access only" > /etc/openssh/bannermotd
systemctl restart sshd
```
> В параметре **AllowUsers** вместо **`Tab`** используется 4 пробела.

</details>

<br/>

## Задание 5

### Между офисами HQ и BR необходимо сконфигурировать IP-туннель

- Сведения о туннеле занесите в отчёт
- На выбор технологии GRE или IP in IP

<br/>

<details>
<summary>Решение</summary>
<br/>

<p align="center"><strong>Таблица туннеля GRE</strong></p>
<table align="center">
  <tr>
    <td align="center">Технология</td>
    <td align="center">Устройство</td>
    <td align="center">Интерфейс</td>
    <td align="center">IPv4/IPv6</td>
    <td align="center" >Маска/Префикс</td>
  </tr>
  <tr>
    <td align="center" rowspan="2">GRE</td>
    <td align="center">HQ-RTR</td>
    <td align="center">tunnel.0</td>
    <td align="center">172.16.0.1</td>
    <td align="center">/30</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">tunnel.0</td>
    <td align="center">172.16.0.2</td>
    <td align="center">/30</td>
  </tr>
</table>

<br/>

#### Создание туннеля на HQ-RTR

Создаем интерфейс **GRE**-туннеля на **HQ-RTR**, назначаем ему IP-адрес и выставляем mtu. Генерируем туннель:
```yml
conf
int tunnel.0
ip address 172.16.0.1/30
ip mtu 1400
ip tunnel 172.16.4.2 172.16.5.2 mode gre
end
wr mem
```

#### Создание туннеля на BR-RTR

Создаем интерфейс **GRE**-туннеля на **BR-RTR**, назначаем ему IP-адрес и выставляем mtu. Генерируем туннель:
```yml
conf
int tunnel.0
ip address 172.16.0.2/30
ip mtu 1400
ip tunnel 172.16.5.2 172.16.4.2 mode gre
end
wr mem
ping 172.16.0.1
```
> При создании туннеля сначала прописывается интерфейс для туннеля самого устройства.

</details>

<br/>

## Задание 6

### Обеспечьте динамическую маршрутизацию: ресурсы одного офиса должны быть доступны из другого офиса. Для обеспечения динамической маршрутизации используйте link state протокол на ваше усмотрение

- Разрешите выбранный протокол только на интерфейсах в ip туннеле

- Маршрутизаторы должны делиться маршрутами только друг с другом

- Обеспечьте защиту выбранного протокола посредством парольной защиты

- Сведения о настройке и защите протокола занесите в отчёт

<br/>

<details>
<summary>Решение</summary>
<br/>

<p align="center"><strong>Таблица динамической маршрутизации OSPF</strong></p>
<table align="center">
  <tr>
    <td align="center">Устройство</td>
    <td align="center">Router ID</td>
    <td align="center">OSPF Neighbour</td>
    <td align="center">Сети</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">1.1.1.1</td>
    <td align="center">2.2.2.2</td>
    <td align="center">172.16.0.0/30, 192.168.100.0/26, 192.168.200.0/28</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">2.2.2.2</td>
    <td align="center">1.1.1.1</td>
    <td align="center">172.16.0.0/30, 192.168.0.0/27</td>
  </tr>
</table>

<br/>

#### Настройка OSPF на HQ-RTR

Создаем процесс **OSPF**, указываем **идентификатор маршрутизатора**, объявляем сети и указываем **пассивные** интерфейсы:
```yml
conf
router ospf 1
router-id 1.1.1.1
network 172.16.0.0/30 area 0
network 192.168.100.0/26 area 0
network 192.168.200.0/28 area 0
passive-interface default
no passive-interface tunnel.0
end
wr mem
```

<br/>

#### Настройка OSPF на BR-RTR
Маршрутизация OSPF на BR-RTR настраивается аналогично, после нее можно произвести проверку OSPF-соседей:
```yml
conf
router ospf 2
router-id 2.2.2.2
network 172.16.0.0/30 area 0
network 192.168.0.0/27 area 0
passive-interface default
no passive-interface tunnel.0
end
wr mem
sh ip ospf neighbor
```

</details>

<br/>

## Задание 7

### Настройка динамической трансляции адресов

- Настройте динамическую трансляцию адресов для обоих офисов.

- Все устройства в офисах должны иметь доступ к сети Интернет

<br/>

<details>
<summary>Решение</summary>
<br/>



#### Настройка NAT на HQ-RTR

Указываем **внутренние** и **внешние** интерфейсы, создаем пул и **правило** трансляции адресов, указывая внешний интерфейс:
```yml
conf
int HQ-RTR-ISP
ip nat outside
int HQ-RTR-CLI
ip nat inside
int HQ-RTR-SRV
ip nat inside
exit
ip nat pool HQ 192.168.100.1-192.168.100.62,192.168.200.1-192.168.200.14
ip nat source dynamic inside-to-outside pool HQ overload interface HQ-RTR-ISP
end
wr mem
```

<br/>

#### Настройка NAT на BR-RTR

Конфигурация:
```yml
conf
int BR-RTR-ISP
ip nat outside
int BR-RTR-SRV
ip nat inside
exit
ip nat pool BR 192.168.0.1-192.168.0.30
ip nat source dynamic inside-to-outside pool BR overload interface BR-RTR-ISP
end
wr mem
```

</details>

<br/>

## Задание 8

### Настройка протокола динамической конфигурации хостов

- Настройте нужную подсеть

- Для офиса HQ в качестве сервера DHCP выступает маршрутизатор HQ-RTR.

- Клиентом является машина HQ-CLI.

- Исключите из выдачи адрес маршрутизатора

- Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR.

- Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV.

- DNS-суффикс для офисов HQ – au-team.irpo

- Сведения о настройке протокола занесите в отчёт

<br/>

<details>
<summary>Решение</summary>
<br/>

<br/>

<p align="center"><strong>Таблица DHCP</strong></p>
<table align="center">
  <tr>
    <td align="center">Подсеть и пул адресов</td>
    <td align="center">DHCP-сервер</td>
    <td align="center">DHCP-клиент</td>
    <td align="center">DNS-сервер</td>
    <td align="center">DNS-суффикс</td>
    <td align="center">Шлюз по умолчанию</td>
  </tr>
  <tr>
    <td align="center">192.168.200.0/28, 192.168.200.1-192.168.200.14</td>
    <td align="center">192.168.200.1 (HQ-RTR)</td>
    <td align="center">192.168.200.2 (HQ-CLI)</td>
    <td align="center">192.168.100.2 (HQ-SRV)</td>
    <td align="center">au-team.irpo</td>
    <td align="center">192.168.200.1</td>
  </tr>
</table>

<br/>

Создаем **пул** для **DHCP-сервера**, настраиваем и привязываем к интерфейсу:
```yml
conf
ip pool HQ-CLI 192.168.200.1-192.168.200.14
dhcp-server 1
pool HQ-CLI 1
mask 26
gateway 192.168.200.1
dns 192.168.100.2
domain-name au-team.irpo
exit
interface HQ-RTR-CLI
dhcp-server 1
end
wr mem
```
> **`pool HQ-CLI 1`** - привязка **пула**

> **`mask 26`** - указание **маски** для выдаваемых адресов из пула

> **`gateway 192.168.200.1`** - указание **шлюза по умолчанию** для клиентов

> **`dns 192.168.100.2`** - указание **DNS-сервера** для клиентов

> **`domain-name au-team.irpo`** - указание **DNS-суффикса** для офиса **HQ**

</details>

<br/>

## Задание 9

### Настройка DNS для офисов HQ и BR

- Основной DNS-сервер реализован на HQ-SRV.

- Сервер должен обеспечивать разрешение имён в сетевые адреса устройств и обратно в соответствии с таблицей 2

- В качестве DNS сервера пересылки используйте любой общедоступный DNS сервер

<br/>

<table align="center">
  <tr>
    <td align="center">Устройство</td>
    <td align="center">Запись</td>
    <td align="center">Тип</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">hq-rtr.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">br-rtr.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">hq-srv.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">hq-cli.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">br-srv.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">moodle.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">wiki.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
</table>

<p align="center"><strong>Таблица 2</strong></p>

<details>
<summary>Решение</summary>
<br/>

#### Настройка конфигурации bind

Временно выставляем dns для HQ-SRV (потом удаляем!), устанавливаем необходимые пакеты:
```yml
echo "nameserver 8.8.8.8" > /etc/net/ifaces/ens18/resolv.conf
systemctl restart network
apt-get update
apt-get install -y nano
apt-get install -y bind bind-utils
```

**Настраиваем файл `/etc/bind/options.conf` на HQ-SRV**
```yml
nano /etc/bind/options.conf
```

```bash
listen-on {127.0.0.1; any; };
listen-on-v6 { ::1; };

forwarders { 8.8.8.8; };

allow-query { any; };

allow-query-cache { any; };

allow-recursion { any; };
```

**Редактируем файл `etc/net/ifaces/ens18/resolv.conf` на HQ-SRV**
```yml
echo "domain au-team.irpo" > /etc/net/ifaces/ens18/resolv.conf
echo "nameserver 127.0.0.1" >> /etc/net/ifaces/ens18/resolv.conf
systemctl restart network
```
<br/>

**Прописываем зоны, в файле `/etc/bind/local.conf`**
```yml
nano /etc/bind/local.conf
```

```yml
zone "au-team.irpo" {
  type master;
  file "au-team.irpo";
};

zone "168.192.in-addr.arpa" {
  type master;
  file "168.192.in-addr.arpa";
};
```


**Копируем файл `cp -r /etc/bind/zone/localdomain /etc/bind/zone/au-team.irpo` и редактируем прямую зону**
```yml
nano /etc/bind/zone/au-team.irpo
```

```bash
$TTL	1D
@  IN	SOA	au-team.irpo. root.au-team.irpo. (
				2025020600	; serial
				12H		; refresh
				1H		; retry
				1W		; expire
				1H		; ncache
			)
		IN	NS	hq-srv.
hq-srv	IN	A	192.168.100.2
hq-rtr	IN	A	192.168.100.1
hq-rtr	IN	A	192.168.200.1
hq-rtr	IN	A	192.168.99.1
br-rtr	IN	A	192.168.0.1
hq-cli	IN	A	192.168.200.2
br-srv	IN	A	192.168.0.2
moodle	IN	CNAME	hq-rtr
wiki	IN	CNAME	hq-rtr
```
**Копируем файл `cp -r /etc/bind/zone/127.in-addr.arpa  /etc/bind/zone/168.192.in-addr.arpa` и редактируем обратную зону**
```yml
nano /etc/bind/zone/168.192.in-addr.arpa
```

```bash
$TTL	1D
@	IN	SOA	au-team.irpo. root.au-team.irpo. (
				2025020600	; serial
				12H		; refresh
				1H		; retry
				1W		; expire
				1H		; ncache
			)
		IN	NS	hq-srv.
		IN	NS	hq-srv.au-team.irpo.
1.0	IN	PTR	br-rtr.au-team.irpo.
2.0	IN	PTR	br-srv.au-team.irpo.
1.100	IN	PTR	hq-rtr.au-team.irpo.
1.200	IN	PTR	hq-rtr.au-team.irpo.
1.99	IN	PTR	hq-rtr.au-team.irpo.
2.100	IN	PTR	hq-srv.au-team.irpo.
2.200	IN	PTR	hq-cli.au-team.irpo.
1.100	IN	PTR	moodle.au-team.irpo.
1.100	IN	PTR	wiki.au-team.irpo.
```

**Задаем пользователя и права доступа на файлы**
```yml
systemctl enable --now bind
cd /etc/bind/zone
chown named:named au-team.irpo
chown named:named 168.192.in-addr.arpa
chmod 755 au-team.irpo
chmod 755 168.192.in-addr.arpa
```
- *Конфигурируем ключи доступа*
```yml
cd /etc/bind/zone
rndc-confgen > /etc/rndckey
```
**Не забываем все сохранить:**
```yml
systemctl restart network
systemctl restart bind
```
**На BR-SRV прописываем:**
```yml
echo "domain au-team.irpo" > /etc/net/ifaces/ens18/resolv.conf
echo "nameserver 192.168.100.2" >> /etc/net/ifaces/ens18/resolv.conf
systemctl restart network
```
**Проверка осуществляется с помощью команды:**
```yml
nslookup moodle
```
или 
```yml
ping moodle
```

</details>

<br/>

## Задание 10

### Настройте часовой пояс на всех устройствах, согласно месту проведения экзамена

<br/>

<details>
<summary>Решение</summary>
<br/>

#### Настройка часового пояса на Alt Linux

Меняем часовой пояс и проверяем его:
```yml
timedatectl set-timezone Asia/Yekaterinburg
timedatectl status
```

<br/>

#### Настройка часового пояса на EcoRouter

Прописываем команды для установки времени и проверки:
```yml
conf
ntp timezone utc+5
do show ntp timezone
end
wr mem
```

</details>

<br/>
