---
title: "Копирование файлов Windows <-> Linux (SCP)"
date: 2022-10-09T13:44:46+07:00
draft: false
categories: ["tools", "linux"]
featured_image: "posts/scp.png"
images: ["posts/scp.png"]
---

Сегодня короткая заметка об одном полезном иструменте под название SCP - утилита для копирования файлов в SSH сессии. Он доступен в Windows 10 как один из инструментов работы с SSH.

<!--more-->

**Копирование файла с компьютера Windows на удаленный компьютер Linux по SSH:**

```sh
scp "C:\dir\file.zip" root@192.168.1.1:~/archives 
```
<small>`file.zip` будет скопирован в каталог `archives` в домашнем каталоге пользователя `root`</small>

Можно копировать файлы используя маски в имени:
```sh
scp "C:\dir\*.zip" root@192.168.1.1:~/archives 
```
<small>все файлы c расширением `zip` будут скопированы в каталог `archives` в домашнем каталоге</small>

С ключем `-r` можно рекурсивно скопировать файлы и каталоги в каталог назначения:
```sh
scp -r "C:\dir\" root@192.168.1.1:~/archives 
```
<small>все файлы и каталоги будут скопированы в каталог `archives` в домашнем каталоге</small>

**Копирование файла с удаленного компьютера Linux на компьютер Windows по SSH:**

```sh
scp.exe root@192.168.1.1:~/archives/file.zip "C:\dir\"
```
<small>Файл `file.zip` будет скопирован в каталог `C:\dir`</small>