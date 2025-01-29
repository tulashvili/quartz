---
title: Git-clone с помощью PAT (Perconal Access Token)
description: 
tags:
  - areas/infra/vcs/git
draft: true
created: 2024-07-02T18:28
updated: 2024-11-26T13:13
type:
  - "[[Deployment]]"
---
[Документация](https://gitlab.dats.tech/help/user/project/settings/project_access_tokens):
- Доступ к репозиторию установлен по ssh
- Возникла ситуация, при которой необходимо клонировать репозиторий именно с помощью `https`, тк коллеги-девопсы сказали, что нельзя по ssh настроить нормально в ci

- Создаем токен и сохраняем его для себя![[2-dev/blog/Screenshot 2024-07-02 at 18.45.10.png]]
- Пробуем подключиться
```
git clone https://git@gitlab.dats.tech/datsteam/php-ext/tgor-integration.git
```
- Когда запросит пароль - указываем созданный ранее токен

Все работает, отлично, но это не решает нашу задачу. Дело в том, что для деплоя разработчики используют [[Deployer]]. При запуске `composer install` выполняется такой флоу:
```
task deploy:setup
task deploy:lock
task deploy:release
task deploy:update_code - подтягивает код из репы (git clone, git pull)
task deploy:shared
task deploy:writable
task deploy:vendors - подтягивает вендоры через композер (тут у нас возникает ошибка)
task deploy:cache:clear
task env:check
task assets:public
task supervisor:stop
task supervisor:update
task messenger:setup-transports
task database:migrate
task clickhouse:migrate
task deploy:symlink
task crontab:install
task deploy:unlock
task deploy:cleanup
task deploy:success
```

На этапе `task deploy:vendors` нас просит ввести пароль для стороннего репозитория, откуда нужно подтянуть зависимости.
Это не проблема - мы просто вводим логин и пароль (токен, полученный ранее) и на вопрос о необходимости сохранить данные мы нажимаем `y`:
![[2-dev/blog/Screenshot 2024-07-03 at 12.56.41.png]]

Исходя из этого у нас образуется решение: сохранить в `/var/www/lgaming/shared` файл `auth.json` и настроить к нему симлинк в каждом релизе `/var/www/lgaming/release/*`

Это неплохое решение, но тут есть одно "но": у нас несколько серверов и на всех будет выполнена команда `composer install` (она будет выполнена локально у разработчика сразу на всех серверах, но флоу останется тот же). А значит операцию из предыдушего абзаца нужно будет повторить на каждом из серверов. Хорошо, если серверов 4-5, а если 15? Это долго и муторно будет делать. Да, можем скопировать получившийся файл с помощью `scp` на другие сервера, но как-будто бы это не лучшее решение.

Другое решение - это добавить переменную в файл `.gitlab-ci.yml` токен, который я создал для "побочного" репозитория. Вот, как это выглядит:
![[2-dev/blog/Screenshot 2024-07-03 at 13.20.49.png]]
А также я создал переменную в основном репозитории:
![[2-dev/blog/Screenshot 2024-07-03 at 13.21.55.png]]
Теперь эту переменную нужно положить в файл gitlab-ci. Например, вот как это сделано для переменной `COMPOSER_TOKEN`:
_____
enable решает траблу
![[Screenshot 2024-09-03 at 14.14.20.png]]
