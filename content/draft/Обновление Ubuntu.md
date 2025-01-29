---
title: Обновление Ubuntu
description: 
tags:
  - areas/infra/linux
draft: true
created: 2024-07-03T18:44
updated: 2024-11-26T13:13
---
Есть сервер, который имеет end-of-life версию операционной системы:
```
otulashvili@vpn118-my-switcherry:~$
hostnamectl
   Static hostname: vpn118-my-switcherry.vma
         Icon name: computer-vm
           Chassis: vm
        Machine ID: d932beb355f74007b0e33f72faa3f820
           Boot ID: 15e3d0895152450a9abf7f7212b25609
    Virtualization: kvm
  Operating System: Ubuntu 18.04.5 LTS
            Kernel: Linux 4.15.0-126-generic
      Architecture: x86-64
```

Дело в том, что инструкции по обновлению end-of-life версии операционной системы и той, у которой еще не истек срок стандартной поддержки, отличаются.
У моей 18.04 срок [[2-dev/blog/Screenshot 2024-07-03 at 19.17.27.png|стандартной поддержки]] истек еще год назад, поэтому мы пойдем по [этой](https://help.ubuntu.com/community/EOLUpgrades/) инструкции

Для начала нам нужно [[Backup дискогового раздела в Ubuntu|сделать backup]]

```
lsb_release -a

Distributor ID: Ubuntu                                                                                                                      

Description:    Ubuntu 18.04.5 LTS                                                                                                          

Release:        18.04                                                                                                                       

Codename:       bionic
```





