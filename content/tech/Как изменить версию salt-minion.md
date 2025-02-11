---
date: 2025-02-11T09:36:00
updated: 2025-02-11T17:15
title: Как изменить версию salt-minion
draft: false
tags:
  - tutorials/saltstack
---
Пробуем достучаться до нужного сервера с мастера:
```sh
sudo salt <недоступный сервер> test.ping
```

Если падает по тайм-ауту, то заходим по ssh на сервер и проверяем логи salt-minion.
Если увидели такую запись:
```sh
salt-minion[1566540]: The master may need to be updated if it is a version of Salt lower than** **3006.7
```
Значит дело в версии миньона. Версию можно посмотреть командой `salt-minion --version`

Далее два варианта получения нужных нам исходников
- Заходим на любой сервер который отвечает на `test.ping` и через `scp` из `/var/cache/apt/archives/` копируем два файла
	- `salt-common_3006.6_amd64.deb, salt-minion_3006.6_amd64.deb`
- или же получаем нужные версии из офф репо:
	  ```
	  wget https://packages.broadcom.com/artifactory/saltproject-deb/pool/salt-common_3006.6_amd64.deb
	  ```
	  ```
	  wget https://packages.broadcom.com/artifactory/saltproject-deb/pool/salt-minion_3006.6_amd64.deb
	  ```
- Устанавливаем salt-common, а потом salt-minion
```sh
sudo dpkg -i salt-common_3006.6_amd64.deb

dpkg: warning: downgrading salt-common from 3007.1 to 3006.6
*******
```

После установки проверяем версию salt-minion:
```sh
salt-minion -V
Salt Version:
          Salt: 3006.6
```

Все готово! :)
