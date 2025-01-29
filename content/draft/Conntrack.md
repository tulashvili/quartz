---
title: 
date: 
description: 
draft: true
tags:
  - areas/infra/linux/fundamentals
  - areas/infra/linux/network/firewall
created: 2024-06-20T13:08
updated: 2024-11-26T13:12
---
Conntrack - часть сетевого стека ядра Linux. 
Она позволяет ядру отслеживать все [[Сетевые сессии в Linux|соединения]] внутри брандмауэра. 
Вот как таблица выглядит на одном из рабочих серверов, проверенном командой `conntrack -L`:
```
tcp      6 5 TIME_WAIT src=192.168.22.103 dst=192.168.22.102 sport=28966 dport=3306 src=192.168.22.102 dst=192.168.22.103 sport=3306 dport=28966 [ASSURED] mark=0 use=1
tcp      6 6 TIME_WAIT src=182.61.13.22 dst=185.26.99.24 sport=44298 dport=22 src=185.26.99.24 dst=182.61.13.22 sport=22 dport=44298 [ASSURED] mark=0 use=1
tcp      6 8 TIME_WAIT src=192.168.22.103 dst=192.168.22.102 sport=29062 dport=3306 src=192.168.22.102 dst=192.168.22.103 sport=3306 dport=29062 [ASSURED] mark=0 use=1
tcp      6 0 TIME_WAIT src=192.168.22.103 dst=192.168.22.102 sport=3624 dport=3306 src=192.168.22.102 dst=192.168.22.103 sport=3306 dport=3624 [ASSURED] mark=0 use=1
udp      17 19 src=185.26.99.24 dst=46.4.54.78 sport=58950 dport=123 src=46.4.54.78 dst=185.26.99.24 sport=123 dport=58950 mark=0 use=1
tcp      6 6 TIME_WAIT src=192.168.22.103 dst=192.168.22.102 sport=28988 dport=3306 src=192.168.22.102 dst=192.168.22.103 sport=3306 dport=28988 [ASSURED] mark=0 use=1
icmp     1 25 src=13.53.44.154 dst=212.224.118.89 type=8 code=0 id=16 src=212.224.118.89 dst=13.53.44.154 type=0 code=0 id=16 mark=0 use=1
tcp      6 598 ESTABLISHED src=192.168.22.103 dst=192.168.22.102 sport=40774 dport=3306 src=192.168.22.102 dst=192.168.22.103 sport=3306 dport=40774 [ASSURED] mark=0 use=1
tcp      6 597 ESTABLISHED src=192.168.22.103 dst=192.168.22.102 sport=28998 dport=3306 src=192.168.22.102 dst=192.168.22.103 sport=3306 dport=28998 [ASSURED] mark=0 use=1
tcp      6 4 TIME_WAIT src=192.168.22.103 dst=192.168.22.102 sport=28958 dport=3306 src=192.168.22.102 dst=192.168.22.103 sport=3306 dport=28958 [ASSURED] mark=0 use=1
tcp      6 1 TIME_WAIT src=192.168.22.103 dst=192.168.22.102 sport=3636 dport=3306 src=192.168.22.102 dst=192.168.22.103 sport=3306 dport=3636 [ASSURED] mark=0 use=1
conntrack v1.4.5 (conntrack-tools): 41 flow entries have been shown.
```
Здесь мы видим ❓❓❓❓:
- `TCP` - протокол ([[TCP (UDP) протокол|TCP/UDP]])
- IP-адрес источника
- порт источника
- IP-адрес назначения
- порт назначения
- состояние соединения

- количество подключений на хост sysctl
	- таблица nf_conntrack и net.nf_conntrack_max - можно глянуть в netdata

Источники:
- [Conntrack tales - one thousand and one flows](https://blog.cloudflare.com/conntrack-tales-one-thousand-and-one-flows)
- [Когда Linux conntrack вам больше не товарищ / Хабр](https://habr.com/ru/companies/nixys/articles/492686/)