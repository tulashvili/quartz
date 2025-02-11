---
title: Как пересобрать nginx с новым модулем
draft: false
tags:
  - tutorials/web/nginx
date: 2025-02-06
updated: 2025-02-08T11:31
---
В ходе [[Настройка forward proxy в Nginx c basic auth с помощью модуля proxy_connect|настройки]] прокси мне понадобилось пересобрать, уже собранный ранее из исходников, nginx.
В моем случае это был модуль `ngx_http_ssl_module`.
Для этого я перехожу в директорию, куда изначально был клонирован nginx и в ней выполняю:
```shell
sudo ./configure --add-module=/usr/local/src/ngx_http_proxy_connect_module --with-http_ssl_module
```
Затем:
```shell
sudo make && make install
```

В моем случае я получил пачку ошибок, которые говорили о том, что моя версия Nginx использует устаревшие API из OpenSSL 3.0:
```shell
error: ‘ENGINE_load_private_key’ is deprecated: Since OpenSSL 3.0
```

Тогда я решил очистить содержимое, которое скомпилировалось ([тут](https://habr.com/ru/articles/211751/) можно подробнее почитать о make ([[Просто о make|сохраненная версия]])):
```sh
make clean

sudo ./configure --add-module=/usr/local/src/ngx_http_proxy_connect_module --with-http_ssl_module --with-cc-opt="-Wno-error=deprecated-declarations"

sudo make && sudo make install
```

Проверяем, какие модули используются в нашем nginx сейчас:
```sh
nginx -V

nginx version: nginx/1.18.0
built by gcc 11.4.0 (Ubuntu 11.4.0-1ubuntu1~22.04) 
built with OpenSSL 3.0.2 15 Mar 2022
TLS SNI support enabled
configure arguments: --add-module=/usr/local/src/ngx_http_proxy_connect_module --with-http_ssl_module --with-cc-opt=-Wno-error=deprecated-declarations
```

Отлично, nginx пересобрался успешно! При этом менять конфигурацию или дополнительно еще что-то мне не пришлось, так как видно, что он был собран в ту же самую директорию, в которую собрался ранее (из вывода `make && make install`):
```sh
test -f '/usr/local/nginx/conf/nginx.conf' \
        || cp conf/nginx.conf '/usr/local/nginx/conf/nginx.conf'
```

