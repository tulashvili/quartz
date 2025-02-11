---
title: Quartz. Решение проблемы с couldn't find remote ref v4
draft: false
tags:
  - tutorials/blog/quartz
  - tutorials/git
date: 2025-01-22
updated: 2025-01-25T11:55
---
Я выполнил базовую настройку своего quartz:

- добавил контент
- создал репозиторий на github
- выполнил push в репозиторий

Теперь я захотел настроить деплой с помощью github actions. В целом [инструкция](https://quartz.jzhao.xyz/hosting#github-pages) незамысловатая:

- добавить `deploy.yaml` в определенную директорию
- в настройках github-репозитория включить Github Pages
- выполнить `npx quartz sync`

Но не все так просто: получаю ошибку:

```
npx quartz sync

 Quartz v4.4.0 

Backing up your content
[main fbbbd8e] Quartz sync: Jan 22, 2025, 8:09 PM
 1 file changed, 1 insertion(+), 1 deletion(-)
Pulling updates from your repository. You may need to resolve some `git` conflicts if you've made changes to components or plugins.
fatal: couldn't find remote ref v4
```

Окей, я попробовал изменить ветку в самом `deploy.yaml` с:

```yaml
name: Deploy Quartz site to GitHub Pages
 
on:
  push:
    branches:
      - v4
```

на:

```yaml
name: Deploy Quartz site to GitHub Pages

on:
  push:
    branches:
      - main
```

Но результата это не дало.  
Затем я пошел в свой аккаунт на github в репозиторий проекта, запустил [тестовый](https://docs.github.com/en/actions/writing-workflows/quickstart) Github Action - он отработал.  
![[Pasted image 20250125115342.png]]

Тогда я понял, что дело точно в названии веток. Создал ветку `v4` из `main`. Сделал рекоммит изменений в `deploy.yaml` - просто убрал пробел и тогда-то и увидел ошибку, из-за которой у меня возникает проблема:  
![[Pasted image 20250125115454.png]]

В [руководстве](![Pasted image 20250122234540.png](app://3886ec6ee970b24754644abfea502a37ecca/Users/omartulashvili/Library/Mobile%20Documents/iCloud~md~obsidian/Documents/Thinking/attachments/Pasted%20image%2020250122234540.png?1737578740425)) как раз сказано, что это может быть одной из проблем. Я удалил environment, запустил заново Github Action и тогда деплой отработал успешно!  
![[Pasted image 20250125115517.png]]
![[Pasted image 20250125115539.png]]
![[Pasted image 20250125115553.png]]