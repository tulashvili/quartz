---
title: Корневая директория Nginx
date: 2024-06-19
description: 
draft: true
created: 2024-06-19T12:50
updated: 2024-11-26T13:12
tags:
  - dictionary/web/nginx
---
```nginx
    server {
      listen 443 ssl;

      client_max_body_size ********;
      server_name ********

      ssl_certificate ********
      ssl_certificate_key ********

      root /var/www/venga/current/public;
```

Корневой каталог для статических файлов сайта.

Когда Nginx получает HTTP-запрос к нашему серверу, он будет искать запрашиваемые файлы в директории `/var/www/venga/current/public`. 

Например, если браузер запрашивает `https://venga.network/index.html`, Nginx будет искать файл `/var/www/venga/current/public/index.html` и отправит его в ответ, если он существует.

*Источник:*
- [Оффициальная документация](https://docs.nginx.com/nginx/admin-guide/web-server/serving-static-content/)
