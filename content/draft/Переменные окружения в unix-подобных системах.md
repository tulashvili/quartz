---
title: 
date: 
description: 
draft: true
tags:
  - areas/infra/linux/fundamentals
created: 2024-06-20T17:55
updated: 2024-11-26T13:12
---
Переменные окружения - это набор пар ключ-значения, которые определяют, где оболочка ищет исполняемые файлы.
Для отображения всех установленных переменных необходимо ввести:
```
printenv
```
❓
но чем отличается от
```
echo $PATH
```

`export PATH=$PATH:$(go env GOPATH)/bin` (временное добавление пути)
