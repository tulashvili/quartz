---
title: Untitled
description: 
tags:
  - areas/infra/web
draft: true
created: 2024-07-08T15:42
updated: 2024-11-26T13:12
---
Как правило, ошибка выглядит так:
```
curl -I https://api.dev.tgor.pro
HTTP/1.1 403 Forbidden
Server: nginx
Date: Mon, 08 Jul 2024 11:35:30 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 146
Connection: keep-alive
```

> [!info] 
> Страница пополняется по мере того, с какими кейсами я сталкиваюсь в своей [[Обо мне|практике]]

## Кейс №1
```
curl -I https://api.dev.tgor.pro
HTTP/1.1 403 Forbidden
Server: nginx
Date: Mon, 08 Jul 2024 11:35:30 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 146
Connection: keep-alive
```
Получаем 403 с:
- из инфраструктуры
- из под VPN, если делаем запрос локально
- при подключении к другому VPN - получаем ожидаемое поведение (401)
Проверено:
- ipset.conf
- nginx.conf
- firewall также разрешает подключение к 443 порту
В процессе решения проблемы также выяснил, что `curl -Ik https://localhost:443` также отдает 403
В логах видим `tgor-frontend-error.log`:
```
2024/07/08 16:07:56 [error] 1039355#1039355: *109971 access forbidden by rule, client: 49.12.111.161, server: dev.tgor.pro, request: "HEAD / HTTP/1.1", host: "116.202.111.5"
2024/07/08 16:08:20 [error] 1039355#1039355: *109974 access forbidden by rule, client: 127.0.0.1, server: dev.tgor.pro, request: "HEAD / HTTP/1.1", host: "localhost"
2024/07/08 16:10:44 [error] 1039355#1039355: *109975 access forbidden by rule, client: 152.32.235.85, server: dev.tgor.pro, request: "GET / HTTP/1.1", host: "116.202.111.5:443"
```
- в ferm ip добавлен
- в fail2ban добавлен

добавил allow в /srv/salt/tg-orchestrator/files/nginx_config
```
{%- if grains.stage == "dev" %}
    {%- for ip in pillar.office_ips %}
    allow {{ ip }};
    {%- endfor %}
    allow 78.47.21.185;
    allow 78.46.230.114;
    allow 176.9.92.90;
    # INFRASTRUC-37435
    allow 49.12.111.161; #dev-lgaming
    allow 49.12.247.30;  #dev-dephouse
    allow 159.69.189.187;#dev-banzai
    allow 88.198.93.104; #dev-venga
    deny all;
{%- endif  %}
```

## Кейс №2
[[2024-09-22]]

В общем я копался на своем сервере и [[Как я НЕ настроил свой собственный VPN-сервер с использованием OpenConnect|пробовал]] там поднять VPN. Чем закончилось можно прочитать в слинкованной заметке, сейчас не об этом.

Я [[Начал проходить курс по Nginx|решил пройти курс]] по Nginx для закрепления знаний и их систематизации.
Ну-с, так как на сервере я пытался поднять VPN и юзал для этого nginx, то тут осталось много мусора. Решаю снести его и установить заново. Но не тут то было - все равно получил ошибки при попытке проверить статус службы после переустановки.
Ладно, решил удалить прям все, что связано с nginx с помощью purge, а также логи и зависимости в `/var/lib`

Окей, nginx завелся. Иду по уроку, отрабатываю самую простую конфигурацию, но и тут затык - с localhost отдается 403, извне вообще по таймауту падает. При этом к ip-адресу, извне, пинги идут. Ну-с, дело точно в фаерволле.

Вспоминаю, что тут работает у меня `ufw`. Отрубаю его. Но все равно какая-то фигня.

Ага, увидел, что некорректный права стоят для моего сайта тестового. Меняю на `www-data`, проверяю - везде работает


