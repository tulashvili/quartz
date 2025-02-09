---
title: Настройка forward proxy в Nginx c basic auth с помощью модуля proxy_connect
draft: false
tags:
  - tutorials/web/proxy
  - tutorials/web/nginx
date: 2024-02-04
---
## Сборка nginx с использованием proxy_connect
Помнишь я [[Настройка forward proxy в Nginx c basic auth|настраивал]] прямо прокси? Так вот это был тестовый сервер, теперь мне понадобилось это сделать на проде, но на проде с идентичным конфигом и способом установки у меня возникала в nginx петля.

Я предположил, что проблема в установленных версиях nginx - вероятно не хватало модуля для прокси в версии, установленной на проде. Тогда я решил собрать вручную nginx c **[ngx_http_proxy_connect_module](https://github.com/chobits/ngx_http_proxy_connect_module)** .

Вот [тут](https://habr.com/ru/articles/680992/) ([[Как я развёртывал прямой proxy на базе nginx|сохраненная статья|]]) можно найти хорошую статью по настройке прямого прокси с помощью этого модуля. Единственное, что:
- я не устанавливал Lua
- я использовал вот такой конфиг `nginx.conf` с резолверами нашего хостера:
```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
worker_rlimit_nofile 15000;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;

    # Настройка forward proxy с использованием proxy_connect
   server {
    listen <port>;
    server_name <domain>;

    error_log /var/log/nginx/nginx_proxy_debug.log debug;

    resolver 91.228.153.88 5.187.2.187;

    proxy_connect;
    proxy_connect_allow all;
    proxy_connect_connect_timeout 10s;
    proxy_connect_read_timeout 10s;
    proxy_connect_send_timeout 10s;

    location / {
        proxy_pass http://$http_host$request_uri;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Скрываем заголовки аутентификации, если не нужен проксируемым сервисам
        proxy_hide_header Authorization;
        proxy_hide_header Proxy-Authorization;
    }
}
}
```

Вот так проверим работу:
```shell
curl -v --proxy http://<domain:port> http://example.com
*   Trying <ip:port>...
* Connected to (nil) (<ip>) port <port> (#0)
> GET http://example.com/ HTTP/1.1
> Host: example.com
> User-Agent: curl/7.81.0
> Accept: */*
> Proxy-Connection: Keep-Alive
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Server: nginx/1.18.0
< Date: Tue, 04 Feb 2025 13:34:04 GMT
< Content-Type: text/html
< Content-Length: 1256
< Connection: keep-alive
< ETag: "84238dfc8092e5d9c0dac8ef93371a07:1736799080.121134"
< Last-Modified: Mon, 13 Jan 2025 20:11:20 GMT
< Cache-Control: max-age=1495
< X-N: S
< 
<!doctype html>
..............
```

Затем я [[Как добавить домены сервера под управление сертификатам certman|выпустил]] сертификаты с помощью certman и добавил в блок `server` несколько новых строк:
```nginx
 ssl_certificate /etc/ssl/private/proxy-tgor-integration.lbeet.tech/fullchain.pem;
    ssl_certificate_key /etc/ssl/private/proxy-tgor-integration.lbeet.tech/privkey.pem;
```

## Пересборка nginx с использованием модуля ngx_http_ssl_module 
При попытке релоаднуть, после внесения изменений, я получил ошибку, говорящую о том, что необходим модуль `ngx_http_ssl_module`. 
Ох и расстроился же я тогда, столько времени убил :) А расстроился, так как nginx я никогда не пересобирал и думал, что придется заново его настраивать, писать снова конфиг и так далее.

По итогу [[Как пересобрать nginx с новым модулем|пересобрал]] nginx, релоаднул его, проверил, что ssl работает:
```sh
curl -v --proxy https://domain:port http://
example.com

* Proxy certificate:
*  subject: CN=<domain>
*  start date: Feb  3 09:58:21 2025 GMT
*  expire date: May  4 09:58:20 2025 GMT
*  subjectAltName: host "(nil)" matched cert's "domain"
*  issuer: C=US; O=Let's Encrypt; CN=E5
*  SSL certificate verify ok.
```

## Подключение базовой авторизации
Теперь осталось только подключить базовую авторизацию. Для этого повторим действия, которые выполняли в [[Настройка forward proxy в Nginx c basic auth#Настройка базовой авторизации|прошлой]] инструкции по настройке forward-proxy.

После настройки базовой авторизации я рестартанул снова nginx, проверил:
```sh
curl -x https://<domain>:<port> -U "user:password" https://google.com

curl: (56) Received HTTP code 401 from proxy after CONNECT
```
Ну а в логах, соответственно:
```sh
no user/password was provided for basic authentication
```

Окей, сделал прям через веб-морду, там авторизация прошла успешно, но я получил:
![[Pasted image 20250206173926.png]]
## Решение проблемы, при которой запрос уходит на HTTP вместо HTTPS
Вообще нужно было бы, конечно, авторизацию подключать в самом конце - когда уже все точно работает... Поэтому пока решил закомментировать строки с авторизацией и решить вопрос без нее

В конфиге я забыл добавить `https`:
```nginx
location / {
        proxy_pass https://$http_host$request_uri;
```
Добавил, релоаднул nginx. Получил ошибку, мол, воркеров мало. Увеличил до 2000.
Снова проверил - все ОК кроме того, что ругается на слишком большой отправленный заголовок:
```sh
*2059 client sent too long header line: "X-Forwarded-For: <ip>
```

Раскоментировал авторизацию, проверил через веб-морду: 
![[Pasted image 20250206175927.png]]

Решил оставить так - уж очень долго я вожусь с этой задачей, думаю, что это не критичная ошибка.
А если критичная - то будет новый пост :)

