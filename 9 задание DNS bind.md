# *demoexam 2025*
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

<br/>

<details>
<summary>Решение</summary>
<br/>

#### Настройка конфигурации bind
Устанавливаем необходимые пакеты:
```yml
apt-get install -y bind bind-utils
```

**Настраиваем файл `/etc/bind/options.conf` на HQ-SRV**
```bash
Listen-on {127.0.0.1; any; };
Listen-on-v6 { ::1; };

forwarders { 8.8.8.8; };

allow-query { any; };

allow-query-cache { localnets; };

allow-recursion { any; };
```

**Редактируем файл `etc/net/ifaces/ens18/resolv.conf` на HQ-SRV**
```bash
domain au-team.irpo
nameserver 127.0.0.1
```

**Прописываем зоны, в файле `/etc/bind/local.conf`**
```bash
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
br-rtr	IN	A	192.168.0.2
moodle	IN	CNAME	hq-rtr
wiki	IN	CNAME	hq-rtr
```
**Копируем файл `cp -r /etc/bind/zone/127.in-addr.arpa  /etc/bind/zone/168.192.in-addr.arpa` и редактируем обратную зону**
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
```bash
systemctl enable --now bind
cd /etc/bind/zone
chown named:named au-team.irpo
chown named:named 168.192.in-addr.arpa
chmod 755 au-team.irpo
chmod 755 168.192.in-addr.arpa
```
- *Конфигурируем ключи доступа*
```bash
cd /etc/bind/zone
rndc-confgen > /etc/rndckey
```
### Не забываем все сохранить
```bash
systemctl restart network
systemctl restart bind
```

**Проверяем с помощью команды:**
```yml
nslookup moodle
```
или 
```yml
ping moodle
```
