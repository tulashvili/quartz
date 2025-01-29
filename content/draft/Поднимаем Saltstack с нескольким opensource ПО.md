---
title: "{{title}}"
description: 
tags: 
draft: true
created: 2024-07-09T22:03
updated: 2024-11-26T13:12
---
Привет! Меня зовут Омар, я системный администратор в компании DatsTeam. Я использую saltstack (далее "salt") уже полгода на ежедневной основе и поэтому решил поделиться практическим пособием по его настройке на своем сервере.

Мы разберем:
- Установку и настройку Saltstack на Ubuntu 22.04
- Напишем свою sls для установки и обновления необходимых нам компонентов
- Установим vaultwarden
- Поуправляем настройкой vaultwarden с помощью salt
- ......

## Перед началом настройки
У меня куплен сервер на DigitalOcean со следующими характеристиками:
1 GB Memory / 10 GB Disk / FRA1 - Ubuntu 22.04 (LTS) x64
## Установка
*Для понимания, что именно мы делаем, я буду разбирать каждую команду на кусочки*

Для начала скачаем GPG-ключ:
```
sudo curl -fsSL -o /etc/apt/keyrings/salt-archive-keyring-2023.gpg https://repo.saltproject.io/salt/py3/ubuntu/22.04/amd64/SALT-PROJECT-GPG-PUBKEY-2023.gpg
```
- **sudo**: запускает команду с правами суперпользователя (root)
- **curl**: Утилита командной строки для передачи данных с или на сервер.
	- -f: Включает проверку на ошибки. Если сервер вернет ошибку HTTP, curl завершит работу.
	- -sS: Включает “тихий” режим, при котором curl не показывает прогресс, но показывает сообщения об ошибках, если они возникают
	- -L: Следует за любыми редиректами, если сервер перенаправляет запрос
	- -o: Указывает имя файла, в который будут сохранены загруженные данные.
- https://repo.saltproject.io/salt/py3/ubuntu/22.04/amd64/SALT-PROJECT-GPG-PUBKEY-2023.gpg: URL, с которого будет загружен GPG ключ

Затем
```
echo "deb [signed-by=/etc/apt/keyrings/salt-archive-keyring-2023.gpg arch=amd64] https://repo.saltproject.io/salt/py3/ubuntu/22.04/amd64/latest jammy main" | sudo tee /etc/apt/sources.list.d/salt.list
```
