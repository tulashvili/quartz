---
date: 2025-02-14T18:08
updated: 2025-02-14T18:08
title: 
draft: true
tags: 
---
1. Проверить список запущенных сервисов и открытых портов. Сохранить инфо в файл:
    
    sudo systemctl | grep running > /home/otulashvili/services.old && sudo netstat -ntlp > /home/otulashvili/ports.old
    
2. Запустить [скрипт](https://drive.google.com/file/d/15y1zOMo3bCHb5jjiu0dyRe1gPFovXz9F/view?usp=sharing "Follow link") и дождаться его выполнения
3. Проверить версию системы и убедиться, что она обновилась (на этом этапе обновиться до 11-ого дебиана)
4. Запустить вышеупомянутый скрипт повторно
5. Проверить версию системы и убедиться, что она обновилась (на этом этапе обновиться до 12-ого дебиана)
6. Убедиться, что все сервисы запущены, а нужные порты открыты (повторить первый шаг без направления вывода в файлы)
7. Попробовать запустить скрипт в третий раз, ожидаю получить “Уже установлен Debian 12, обновление не требуется, реактивирую сервисы”
8. Если какие-то сервисы/порты не запущены/закрыты, запустить и устранить ошибки, если таковые будут