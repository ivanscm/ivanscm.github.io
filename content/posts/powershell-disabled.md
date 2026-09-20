---
title: "Как включить выполнение сценариев PowerShell"
date: 2020-04-18T13:55:44+07:00
draft: false
type: "posts"
categories: ["work", "technologies"]
featured_image: "posts/powershell-disabled.png"
images: ["posts/powershell-disabled.png"]
---
При переносе этого блога на хостинг Firebase я столкнулся с ошибкой PowerShell, которая говорила о невозможности загрузки файла, из-за которой у меня отказывались работать инструменты firebase-tools.

Как оказалось, политикой выполнения скриптов по умолчанию запрещается выполнять скрипты. В заметке приводится решение.
<!--more-->  

```cmd
PS C:\_projects\ivanscm.name> firebase init
firebase : Невозможно загрузить файл C:\Users\ivans\AppData\Roaming\npm\firebase.ps1, так как выполнение сценариев откл
ючено в этой системе. Для получения дополнительных сведений см. about_Execution_Policies по адресу https:/go.microsoft.
com/fwlink/?LinkID=135170.
строка:1 знак:1
+ firebase init
+ ~~~~~~~~
    + CategoryInfo          : Ошибка безопасности: (:) [], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```

## Выключение запрета выполнения сценариев
Для решения проблемы достаточно выполнить команду `Set-ExecutionPolicy unrestricted` и выбрать необходимую опцию. В моем случае я выбрал <kbd>A</kbd>(это небезопасно!), но можно и рекомендуется выбирать <kbd>Y</kbd>, что бы система каждый раз спрашивала разрешение на запуск скриптов.
```cmd
PS C:\_projects\ivanscm.name> Set-ExecutionPolicy unrestricted

Изменение политики выполнения
Политика выполнения защищает компьютер от ненадежных сценариев. Изменение политики выполнения может поставить под
угрозу безопасность системы, как описано в разделе справки, вызываемом командой about_Execution_Policies и
расположенном по адресу https:/go.microsoft.com/fwlink/?LinkID=135170 . Вы хотите изменить политику выполнения?
[Y] Да - Y  [A] Да для всех - A  [N] Нет - N  [L] Нет для всех - L  [S] Приостановить - S  [?] Справка
(значением по умолчанию является "N"):A
```

## Включение запрета выполнения сценариев
Для включения запрета выполните `Set-ExecutionPolicy Restricted`.  

Ознакомится подробнее можно в документации на [сайте Microsoft](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7).