---
title: Как добавить домены сервера под управление сертификатам certman
draft: false
tags:
  - tutorials/web/ssl/certman
date: 
description: Пока сырая, не подробная версия
---
- Добавляем сервер на "мастере", где запущен сам сертман, в `/etc/hosts`
- Создаем пользователя `certman` на "слейв" сервере
- Добавляем публичный ключ с "мастер" сервера на "слейв"
- Разрешить ему в судо дергать сервисы, для этого добавить в `sudoers`:
```shell
certman ALL=(ALL:ALL) NOPASSWD: /usr/sbin/nginx *
certman ALL=(ALL:ALL) NOPASSWD: /usr/local/sbin/certman_post_hook
```
- И кинуть файл на наш "слейв" сервер `/usr/local/bin/certman_post_hook`