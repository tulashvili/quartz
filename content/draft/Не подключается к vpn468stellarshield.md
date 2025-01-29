---
title: 
date: 
description: 
draft: true
tags:
  - 
created: 2024-06-19T20:08
updated: 2024-11-26T13:12
---
#project/infra/work_log/dats_team 

в логах:
[chatgpt.com/share/e758328e-e7dd-4e00-b1f7-b9b8adc3778a](https://chatgpt.com/share/e758328e-e7dd-4e00-b1f7-b9b8adc3778a)

- 500 порт слушает 
charon  839 root   14u  IPv6  27845      0t0  UDP *:isakmp
charon  839 root   16u  IPv4  27847      0t0  UDP *:isakmp

- в ferm_vpn.config заменил все eth0 на ens3 для раскатки
- раскатил - заработало