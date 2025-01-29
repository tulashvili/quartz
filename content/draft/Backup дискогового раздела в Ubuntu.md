---
title: Backup дискогового раздела в Ubuntu
description: 
tags:
  - areas/infra/linux/backup
draft: true
created: 2024-07-03T19:30
updated: 2024-11-26T13:12
---
Иду по [этой](https://www.howtoforge.com/how-to-install-and-use-backuppc-backup-software-on-ubuntu-2004/) инструкции.
Backuppc позволяет выполнить backup конкретных файлов и каталогов. Нам же нужно сделать backup всей системы.
Поэтому используем `rsync`
сделал запрос chatgpt, он посоветовал вот что:

Поэтому тестщу на test-vault-agent бэкап с помощью dublicity