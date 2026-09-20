---
title: "Как сбросить пароль администратора в ProccessWire"
date: 2020-04-12T18:40:34+07:00
draft: false
type: "posts"
categories: ["technologies"]
images: ["site-feature-image.png"]
---
Поместите данный код в шаблон страницы и откройте эту страницу в браузере.
<!--more-->
```php
<?php
$u = $users->get('admin'); // или необходимый вам логин
$u->of(false); 
$u->pass = 'новый-пароль';
$u->save();
```
