---
title: Создать cron в Linux
draft: false
tags:
  - dictionary/linux
date: 2025-01-14
---
- Для начала нужно определиться с пользователем, под которым будет выполняться наш cron

> [!INFO] 
> посмотреть всех пользователей в системе можно с помощью  `ls /etc/passwd`

- Определившись с пользователем выполняем `sudo su <user>`
- `crontab -e`
- нам будет предложено выбрать редактор, который будет использоваться для редактирования нашего cron
    - я выбрал рекомендуемый - `nano`
- С синтаксисом cron-задания можно ознакомиться прямо в терминале после выбора редактора
- Я ввожу такие параметры времени:
- `*/10 * * * *` - то есть мой скрипт будет выполняться каждые 10 минут
- Затем я ввожу путь к моему скрипту
- По итогу получается такой cron:

```
   */10 * * * * sudo /home/otulashvili/auto_change_permissions.sh
```

- Для логгирования можем добавить вывод в `/var/log/cron.log 2>&1`

```
*/15 * * * * sudo /home/otulashvili/auto_change_permissions.sh >> /var/log/cron.log 2>&1
```

## Если cron выполняться не хочет

- Добавить в начало `crontab -e` строку:

```
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

- Cron не может получить доступ к `/var/log/cron.log`

```
From: root@*** (Cron Daemon)
To: otulashvili@f1-***
Subject: Cron <otulashvili@f1-***> sudo /home/otulashvili/auto_change_permissions.sh >> /var/log/cron.log 2>&1 (failed)
MIME-Version: 1.0
Content-Type: text/plain; charset=UTF-8
Content-Transfer-Encoding: 8bit
X-Cron-Env: <SHELL=/bin/sh>
X-Cron-Env: <HOME=/home/otulashvili>
X-Cron-Env: <PATH=/usr/bin:/bin>
X-Cron-Env: <LOGNAME=otulashvili>
Message-Id: <E1tXeaX-0004ZA-CC@f1-***>
Date: Tue, 14 Jan 2025 13:56:01 +0300

/bin/sh: 1: cannot create /var/log/cron.log: Permission denied
```

- Тогда создаем руками touch /var/log/cron.log и меняем овнера sudo chown otulashvili:root cron.log