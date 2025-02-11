---
title: Скрываю заголовок поста в loop.hbs
draft: false
tags:
  - tutorials/blog/ghost
date: 2025-01-18
updated: 2025-02-05T01:00
---
Возникло желание скрыть заголовок поста для определенного тега. Полез на форумы искать готовое решение, тк с frontend частью у меня пока не очень - не увенчалось успехом.

Тогда решил полезть в документацию ghost, но там тоже мне показалось слишком долго разбираться для реализации такой простой фичи.
И тогда я решил открыть инспектор браузера и скрыть заголовок самостоятельно, в коде.
## Отображение текста 
По итогу мне удалось найти необходимый class в `<website_dir>/content/themes/attila` и изменить тему так, чтобы заголовок не отображался для постов:
```handlebars
<div class="extra-pagination">
{{pagination}}

</div>

{{#foreach posts visibility="all"}}

<article class="{{post_class}}">
	<div class="inner">
		<div class="box post-box">
			{{#if featured}}
				<span class="post-feature">
					<i class="icon icon-star">{{> "icons/icon-star"}}</i>
				</span>
			{{/if}}
			{{#has tag="micro"}}
			<!-- Пусто, ничего не отображается -->
			{{else}}
			<h2 class="post-title"><a href="{{url}}">{{{title}}}</a></h2>
			{{/has}}
			<span class="post-meta">
				{{t "By"}}
				{{authors separator=", "}}
				{{#primary_tag}}{{t "in"}} <a class="post-meta-tag" href="{{url}}">{{name}}</a>{{/primary_tag}}
				{{t "on"}}
				<time datetime="{{date format='DD-MM-YYYY'}}">{{date format="DD MMM YYYY"}}</time>
			</span>
			<p class="post-excerpt">{{excerpt}}&hellip;</p>
		</div>
	</div>
</article>

{{/foreach}}

{{pagination}}

По сути, я добавил условие has и комментарий для того, чтобы заголовок не отображался (подсмотрел тут): {{#has tag="micro"}}

<!-- Пусто, ничего не отображается -->

{{else}}

<h2 class="post-title"><a href="{{url}}">{{{title}}}</a></h2>
{{/has}}
```

На выходе получилось вот что:
![alt text](image-5.png)

## Решение проблемы с обрезанием текста
С короткими сообщениями все работает нормально, а вот длинные обрезаются.
![alt text](image-6.png)

Для решения этой проблемы я добавил в тот же `loop.hbs` вот что:


```handlebars
{{#has tag="micro"}}

<section class="post-content">
  {{content}}
</section>
{{else}}
<p class="post-excerpt">{{excerpt}}&hellip;</p>
{{/has}}
```

На выходе вместо огрызков у нас получается пост полноценный пост:
![alt text](image-7.png)

## Что дальше
Дальше хочу реализовать возможность публикации прямо с телефона. В идеале, хотелось бы, иметь нативное apk-приложение, но так как у меня пока iphone, то буду делать простую веб-морду с двумя формами:
- Токен
- Сообщение
