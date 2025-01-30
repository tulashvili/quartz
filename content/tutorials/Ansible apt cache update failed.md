---
created: 2024-09-17T14:24
updated: 2025-01-16T21:12
tags:
  - tutorials/ansible
draft: false
title: Ansible apt cache update failed
date: 2024-09-17
---
```
TASK [add saltstack key] ********************************************************************************
ok: [185.44.206.14]

TASK [add saltstack repo] *******************************************************************************
fatal: [185.44.206.14]: FAILED! => {"changed": false, "msg": "apt cache update failed"}
```

В `init.yml` следующее:
```
  - name: add saltstack key
    apt_key:
      url: "https://repo.saltproject.io/salt/{% if ansible_distribution == 'Debian' %}py3/{{ ansible_distribution | lower }}/{{ ansible_distribution_major_version }}{% else %}py3/{{ ansible_distribution | lower }}/{{ ansible_distribution_version }}{% endif %}/amd64/latest/SALT-PROJECT-GPG-PUBKEY-2023.gpg"
  - name: add saltstack repo
    apt_repository:
      filename: saltstack
#      repo: "deb https://repo.saltproject.io/salt/{% if ansible_distribution == 'Debian' %}py3/{{ ansible_distribution | lower }}/{{ ansible_distribution_major_version }}{% else %}py3/{{ ansible_distribution | lower }}/{{ ansible_distribution_version }}{% endif %}/amd64/latest/ {{ ansible_distribution_release }} main"
      repo: "deb https://repo.saltproject.io/salt/{% if ansible_distribution == 'Debian' %}py3/{{ ansible_distribution | lower }}/{{ ansible_distribution_major_version }}{% else %}py3/{{ ansible_distribution | lower }}/{{ ansible_distribution_version }}{% endif %}/amd64/minor/3006.2/ {{ ansible_distribution_release }} main"
```

Покопался в интернетах, попробовал руками:
```
apt clean
apt update
```
Никаких ошибок, все в порядке. Стоит последний debian 12, salt его поддерживает.

Решил пойти путем костылей и руками стянул, согласно [инструкции](https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/debian.html#install-salt-on-debian-12-bookworm-amd64), ключ и репозиторий.
Убрал из `init.yml` строки, на которых фейлилось, предварительно сделав бэкап, запустил ansible `sudo ./init-batch.bash list_example` и все прошло ОК.

Костыль, но проблема единичная, поэтому жить можно. Мы молодцы!
![gif](https://media1.giphy.com/media/1xkufRJ16wyov1o5yZ/giphy.gif?cid=16a6abc21yzhy0cb88anrut3qxq3gp86lhqh1ukp0zkorlx0&ep=v1_gifs_search&rid=giphy.gif&ct=g)

