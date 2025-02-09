---
title: "Как я развёртывал прямой proxy на базе nginx"
source: "https://habr.com/ru/articles/680992/"
author:
  - "[[egorsergey]]"
published: 2022-08-05
created: 2025-02-04
description: "С чего всё начиналось Ты, как специалист в области IT, скорее всего столкнулся с проблемой недоступности некоторых профильных зарубежных ресурсов и наверняка подумал о том, как это дело исправить. Но..."
tags:
  - "clippings"
---
## С чего всё начиналось

Ты, как специалист в области IT, скорее всего столкнулся с проблемой недоступности некоторых профильных зарубежных ресурсов и наверняка подумал о том, как это дело исправить. Но использовать "бесплатный" или сторонний сервис кажется небезопасным или не даёт нужную ширину канала. У тебя есть навыки работы с Linux и свой сервер где нибудь за бугром. Почему бы собственно говоря не сделать свой прямой прокси?

Скорее всего ты посмотришь в сторону SOCKS5 сервера и даже запустишь его в контейнере, всё будет работать, но при этом если ты используешь Windows + любой браузер с расширением для прокси (я использую Switchy Omega), ты столкнёшься с двумя вариантами:

1. Ты будешь поднимать SOCKS5 без аутентификации. Следовательно наш прокси будет открыт для всего света и нашедшие его люди могут использовать его по своему усмотрению;
2. Как умный инженер, ты сделаешь это с аутентификацией. Но тут есть проблема - Windows не умеет в SOCKS5 с аутентификацией. Я пытался найти решение этой проблемы, но у меня не получилось найти информацию, как это сделать. Как вариант использовать Tor Browser, но мне лично его использование не кажется удобным.

## А почему бы не использовать nginx?

Ни для кого не секрет, что nginx прекрасно из коробки умеет быть обратным прокси, и не без этого получил свою популярность. Но вот быть прямым прокси сервером из коробки он не умеет, для этого придётся его пересобрать! Собственно говоря ниже пойдёт речь о том, как научить его это делать.

## Подготовка

Нам потребуется:

1. Linux сервер за пределами необъятной. Ubuntu, Centos или что вам там нравится. Без разницы;
2. Доменное имя, которое будет ссылаться на наш сервер;
3. TLS сертификат (очень желательно, но можно без него). Его получение выходит за рамки данной статьи. Лично я использую [acme.sh](https://github.com/acmesh-official/acme.sh).

## За работу!

Я буду производить настройку на Ubuntu 20.04, вы же вольны использовать всё, что вам душе угодно, но там могут быть свои нюансы. Если вы пойдёте этим путём, то полагаю, вы сами сможете применить эту инструкцию в другом дистрибутиве.

Заблаговременно установим в систему необходимые для сборки пакеты:

`apt install -y make build-essential apache2-utils libpcre++-dev libssl-dev zlib1g-dev`

Создам директорию для "сборки" проекта, в которой будут храниться все временные файлы.

`mkdir build && cd build`

Теперь отправная точка для каждого шага именно эта директория.

1. Скачиваем nginx и разархивируем загруженный архив

`wget https://nginx.org/download/nginx-1.21.6.tar.gz   tar -xzf nginx-1.21.6.tar.gz`
2. Клонируем следующие репозитории, они нам все будут нужны

`git clone https://github.com/chobits/ngx_http_proxy_connect_module   git clone https://github.com/openresty/lua-nginx-module   git clone https://github.com/vision5/ngx_devel_kit   `
3. Подготовка для Lua модуля:

1. Необходимо скачать, разархивировать и установить в систему **LuaJIT**.

`git clone https://github.com/openresty/luajit2   cd luajit2   make && make install`

После этого необходимо создать две необходимые переменные окружения

`export LUAJIT_LIB=/usr/local/lib/   export LUAJIT_INC=/usr/local/include/luajit-2.1/`
2. Установим в систему **lua-resty-core** и **lua-resty-lrucache**. Поочерёдно перейдём в клонированные репозитории и выполним `make install`

1. `git clone https://github.com/openresty/lua-resty-core   cd lua-resty-core   make install`
2. `git clone https://github.com/openresty/lua-resty-lrucache   cd lua-resty-lrucache   make install`
4. Переходим к настройке модуля **ngx\_http\_proxy\_connect\_module**

Согласно документации этого модуля мы должны перейти в директорию с nginx и выполнить команду, в которой указываем путь к патчу, который соответствует нашей версии nginx. Эта информация есть в README этого модуля.

`cd nginx-1.21.6 && patch -p1 <` `/etc/nginx/modules/ngx_http_proxy_connect_module/patch/proxy_connect_rewrite_102101.patch`

![Успешный результат выполнения должен быть примерно таким](https://habrastorage.org/r/w1560/getpro/habr/upload_files/65d/1ed/a13/65d1eda1379451b730ef8934bd794e98.png)

Успешный результат выполнения должен быть примерно таким
5. Теперь запускаем команду configure. В указанном примере стоит обратить внимание на пути к новым модулям (первые три ключа). Они у вас могут быть другими. Также другие ключи взяты из пакета, который устанавливается из стандартного репозитория Ubuntu. Из этого можно много чего выкинуть, но большинству эту не надо.

```bash
./configure --add-module=/root/build/ngx_devel_kit \
	--add-module=/root/build/ngx_http_proxy_connect_module \
	--add-module=/root/build/lua-nginx-module \
	--prefix=/etc/nginx \
	--sbin-path=/usr/sbin/nginx \
	--modules-path=/usr/lib/nginx/modules \
	--conf-path=/etc/nginx/nginx.conf\
	--error-log-path=/var/log/nginx/error.log \
	--http-log-path=/var/log/nginx/access.log\
	--pid-path=/var/run/nginx.pid\
	--lock-path=/var/run/nginx.lock \
	--http-client-body-temp-path=/var/cache/nginx/client_temp \
	--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
	--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
	--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
	--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
	--user=nginx \
	--group=nginx \
	--with-compat \
	--with-file-aio \
	--with-threads \
	--with-http_addition_module \
	--with-http_auth_request_module \
	--with-http_dav_module \
	--with-http_flv_module \
	--with-http_gunzip_module \
	--with-http_gzip_static_module \
	--with-http_mp4_module \
	--with-http_random_index_module \
	--with-http_realip_module \
	--with-http_secure_link_module \
	--with-http_slice_module \
	--with-http_ssl_module \
	--with-http_stub_status_module \
	--with-http_sub_module \
	--with-http_v2_module \
	--with-mail \
	--with-mail_ssl_module \
	--with-stream \
	--with-stream_realip_module \
	--with-stream_ssl_module \
	--with-stream_ssl_preread_module
```
![Что-то подобное вы должны будете увидеть при успешном выполнении команды](https://habrastorage.org/r/w1560/getpro/habr/upload_files/c43/648/9f3/c436489f3be73e97bd98f5df425f1f5a.png)

Что-то подобное вы должны будете увидеть при успешном выполнении команды
6. Осталось запустить

`make && make install`

## Настройка nginx

Для упрощения инструкции все действия буду производится под пользователем **root**, в частности запуск демона и создание директорий.

1. Создадим директорию `mkdir -p /var/cache/nginx`
2. Нужно создать файл для запуска nginx, как сервис

`vim /lib/systemd/system/nginx.service`

Со следующим содержимым

```
[Unit]
Description=A high performance web server and a reverse proxy server
Documentation=man:nginx(8)
After=network.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -q -g 'daemon on; master_process on;'
ExecStart=/usr/sbin/nginx -g 'daemon on; master_process on;'
ExecReload=/usr/sbin/nginx -g 'daemon on; master_process on;' -s reload
ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /run/nginx.pid
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target
```

Теперь обязательно нужно выполнить. Без этого systemd не узнает о нашем сервисе.

`systemctl daemon-reload`
3. Отредактируем файл /etc/nginx/nginx.conf и приведём его примерно к такому содержанию

```nginx
# /etc/nginx/nginx.conf
user root;
worker_processes  1;
pid /run/nginx.pid;events {
    worker_connections  1024;
}http {
    lua_package_path "/usr/local/lib/lua/?.lua;;"; #обазательный параметр!
    #lua_load_resty_core off;    include       mime.types;
    default_type  application/octet-stream;    sendfile        on;    keepalive_timeout  65;    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```
3. Сгенерируем файл аутентификации. Этот файл будет использоваться сервером для проверки учетных данных. И при настройке прокси в браузере нужно будет указать пользователя *proxyuser* и пароль, который будет введён при выполнении кода ниже.

`mkdir -p /etc/nginx/auth   htpasswd -c /etc/nginx/auth/htpasswd proxyuser`
4. Создадим директорию с конфигурациями сайтов

`mkdir -p /etc/nginx/sites-enabled`
5. Осталось создать файл конфигурации в созданной выше директории с таким содержимым. Посмотрите комментарии в коде, если будете делать конфигурацию без сертификата.

`vim /etc/nginx/sites-enabled/fw-proxy`

```nginx
# /etc/nginx/sites-enabled/fw-proxy    server {
	# Если без сертифката, то listen 80;
	listen 443 ssl;
	server_name proxy.example.com;				# Если без сертификата, то убрать или закомментировать два нижних параметра
	ssl_certificate <path to certificate>/fullchain.pem;
	ssl_certificate_key <path to ptivate key>/key.pem;	auth_basic "Server auth";
	auth_basic_user_file /etc/nginx/auth/htpasswd;	# transfer Proxy-Authorization header to Authorization header
	rewrite_by_lua_file /etc/nginx/auth/proxy_auth.lua;	server_tokens off;	resolver 8.8.8.8 ipv6=off;	proxy_connect;
	proxy_connect_allow all;
	proxy_connect_connect_timeout 10s;
	proxy_connect_read_timeout 10s;
	proxy_connect_send_timeout 10s;	location / {
		#proxy_http_version 1.1;
		proxy_pass http://$host;
		proxy_set_header Host $host;		# If backend wont check Auth header, we should not pass the user/password.
		proxy_hide_header Authorization;
		proxy_hide_header Proxy-Authorization;
	}
}
```
6. Проверки работоспособности

Проверяем конфигурацию nginx командой `nginx -t`  

![Конфигурация успешно проверена](https://habrastorage.org/r/w1560/getpro/habr/upload_files/fbe/be6/5d5/fbebe65d53b8f7564ebff07d4faf378d.png)

Конфигурация успешно проверена

Проверим подключение утилитой curl

![](https://habrastorage.org/r/w1560/getpro/habr/upload_files/2fc/f24/8e8/2fcf248e8d067c800d99a3f4da33ec23.png)

## Настройка браузера

После успешного завершения предыдущих шагов можно переходить к настройке браузера.

1. Надо установить расширение Switchy Omega из любого удобного магазина расширений;
2. Зайти в его настройки и создать новый прокси сервер. Как на скриншоте ниже, не забыв указать данные пользователя в "замочке" справа.

![](https://habrastorage.org/r/w1560/getpro/habr/upload_files/b63/966/050/b6396605088e7e5c83a1f5d4230c4806.png)
3. Осталось перейти в профиль **switch** и сопоставить необходимый сайт с прокси сервером и радоваться жизни. Расширение автоматически будет ходить на указанные сайты через прокси. Естественно в расширении немало возможностей по кастомизации поведения.

## Заключение

В заключении хотелось бы написать, что скорее всего есть более элегантные варианты решить эту проблему, но я решил пойти именно этим путём, потому что другого не увидел. В данной инструкции конечно же можно отказаться от использования TLS и базовой аутентификации. Благодаря этому можно будет не заморачиваться с Lua. И собирать nginx только с ngx\_http\_proxy\_connect\_module. И даже есть готовые образы для создания контейнера. Но так не секьюрно, а цель была именно в том, чтобы максимально защитить прокси от посягательств извне.