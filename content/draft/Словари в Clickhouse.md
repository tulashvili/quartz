---
title: 
date: 
description: 
draft: true
created: 2024-06-19T11:52
updated: 2024-11-26T13:12
tags:
  - db/nosql/clickhouse
---
Насколько я понимаю, словари - это что-то вроде кэша. Какие-либо постоянные данные, которые редко меняются.

В примере ниже с помощью `show grants` мы получили список доступных разрешения для нашего пользователя:
```
|GRANT SELECT ON blogs.* TO play|
|2|GRANT dictGet ON blogs.country_iso_codes TO play|
|3|GRANT dictGet ON blogs.country_polygons TO play|
|4|GRANT dictGet ON blogs.stations_dict TO play|
|5|GRANT dictGet ON blogs.stations_dict_by_id TO play|
|6|GRANT SELECT ON default.* TO play|
|7|GRANT SELECT ON git_clickhouse.* TO play|
|8|GRANT SELECT ON mgbench.* TO play|
|9|GRANT SELECT ON system.asynchronous_metric_log TO play|
|10|GRANT SELECT ON system.dashboards TO play|
|11|GRANT SELECT ON system.events TO play|
|12|GRANT SELECT ON system.metric_log TO play|
|13|GRANT SELECT ON system.metrics TO play|
```
Здесь мы видим 4 словаря:
- `country_iso_codes`
- `country_polygons`
- `stations_dict`
Все они находятся в базе данных `blogs`.

Если посмотреть, какие данные в них находятся, то мы убедимся в заявленном мной, и оффициальной документацией Clickhouse, утверждении - это какие-либо постоянные данные, которые могут быть переиспользованы для получения каких-либо данных в клиентском приложении.
Выполним запрос: `SELECT * FROM blogs.country_codes`
И получим:
```
1	France, Metropolitan	-
2	United States Minor Outlying Islands	-
3	Virgin Islands (UK)	-
4	Virgin Islands (US)	-
5	Western Samoa	-
6	World	-
7	Zaire	-
8	Aruba	AA
9	Antigua and Barbuda	AC
10	United Arab Emirates	AE
11	Afghanistan	AF
12	Algeria	AG
13	Azerbaijan	AJ
```

❓❓❓
Таблицы blogs.country_codes нет в списке словарей, но по факту это словарь. Почему?
❓❓❓

*Источники*
- *Для выполнения запросов использовался [Clickhouse Playground](https://play.clickhouse.com/play?user=play)*
- *Понятная [статья](https://bigdataschool.ru/blog/news/clickhouse/clickhouse-dictionaries.html) с примерами*
- *[Официальная документация](https://clickhouse.com/docs/en/sql-reference/dictionaries)*
