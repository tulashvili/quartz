---
title: 
date: 
description: 
draft: true
tags:
  - areas/infra/network/vpn
created: 2024-06-20T07:52
updated: 2024-11-26T13:13
---
IPSec - это группа [[Сетевые протоколы|протоколов]] для защиты соединений между устройствами.
Создается туннель, который обеспечивает зашифрованную передачу пакетов

Пример конфига:
```
conn IKEv2-MSCHAPv2-Apple-Swchconnect

  leftid=vpn400.swchconnect.com

  leftcert=/opt/letsencrypt/certs/vpn400.swchconnect.com/fullchain.pem

  leftsubnet=0.0.0.0/0

  leftfirewall=no

  leftsendcert=always

  rightsourceip=172.28.128.0/20

  rightdns=8.8.8.8,8.8.4.4

  rightsendcert=never

  eap_identity=%identity

  rightauth=eap-radius

  keyexchange=ikev2

  auto=add
```

conn IKEv2-MSCHAPv2-Apple-Swchconnect - Название конфигурации
- leftid - идентификатор для локальной стороны сервера
	- ![[2-dev/blog/Screenshot 2024-07-18 at 14.06.10.png]]
leftcert - путь к серту на сервере

leftsubnet - весь трафик через впн

leftfirewall - откл автодобавление правил для фаервола для этого конфига

leftsendcert - сервер отправляет свой серт клиенту

rightsourceip - Диапазон IP-адресов, которые будут выданы клиентам

rightdns - Список DNS-серверов для клиентов

rightsendcert - Указывает, что клиентам не требуется отправлять свои сертификаты серверу

eap_identity - что-то с eap, хз

rightauth - клиент аутентифицируется с помощью eap и radius

keyexchange - для обмена ключами юзаем IKEv2

auto - соединение не будет установлено автоматически, пока не клацнешь кнопку в прилке

Источники:
- [What is IPsec? How Does IPsec Work? - Huawei](https://info.support.huawei.com/info-finder/encyclopedia/en/IPsec.html)
- Я
- 
