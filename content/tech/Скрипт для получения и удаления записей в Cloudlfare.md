---
title: Скрипт для получения и удаления записей в Cloudlfare
draft: false
tags:
  - tutorials/web/cloudflare
  - coding/python
date: 2025-01-19
updated: 2025-01-23T09:36
---

https://github.com/tulashvili/get_and_del_records_in_cf/tree/main

На работе возникла необходимость перевести огромную пачку (около 150) сайтов с одного технического домена на другой. Так как все домены находятся в Cloudlfare, то, недолго думая, я решил написать скрипт на Python, который сможет получать домены и удалять их при необходимости.

Сначала скрипт просто получал домены. Затем я подумал, что нужно как-то их фильтровать, так как в этом аккаунте Cloudlfare много других доменов, которые не относятся к этому проекту, поэтому добавил фильтрацию на основе введенных слов пользователем

По итогу скриптом я доволен, хотя и понимаю, что часто я прибегал к помощи ChatGpt, тк сталкивался с проблемами, ответа на которые мне не удавалось найти.
