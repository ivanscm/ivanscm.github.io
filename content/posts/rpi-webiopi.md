---
title: "Управление освещением и голосовое оповещение о погоде на Raspbery Pi"
date: 2020-04-25T23:01:02+07:00
draft: false
type: "posts"
categories: ["linux", "technologies"]
featured_image: "posts/rpi-webiopi.png"
images: ["posts/rpi-webiopi.png"]
---
Управление освещением и голосовое оповещение о погоде. Пример кода и электрическая схема подлючения реле к Raspberry Pi.
<!--more-->
Предварительно установите mplayer и alsa:

```bash
sudo apt-get install alsa-utils
sudo apt-get install mplayer
```

Также установите pip для Python, для легкой установки пакетов. И затем c его помощью установите feedparser. Рассказывать не буду, проще найти информацию по запросу «install pip python» в поисковике.

### Код для скрипта WebIOPi
```py
# заставляем работать с кодировкой utf8, необходимо для гугло транслита
# -*- coding: utf-8 -*-
# импорт модулей
import webiopi
import subprocess
import feedparser

# получаем GPIO
GPIO = webiopi.GPIO

# константы на нумерацию портов GPIO
LIGHT = 23
BUTTON1 = 17
BUTTON2 = 22

def setup():
    # конфигурируем пины GPIO
    GPIO.setFunction(LIGHT, GPIO.OUT)
    GPIO.setFunction(BUTTON1, GPIO.IN)
    GPIO.setFunction(BUTTON2, GPIO.IN)

def loop():
        # ловим нажатие кнопки 1
        if (GPIO.digitalRead(BUTTON1) == GPIO.HIGH):
                # инвертируем значение на пине (программный свитч)
                GPIO.digitalWrite(LIGHT, not GPIO.digitalRead(LIGHT))
        # ловим нажатие кнопки 2
        if (GPIO.digitalRead(BUTTON2) == GPIO.HIGH):
                # предупреждаем о задержке
                subprocess.call(["mplayer", "http://translate.google.com/translate_tts?tl=ru&ie=UTF-8&q=Пожалуйста, ожидайте, запрашиваю погоду."])
                # вытаскиваем погоду
                d = feedparser.parse('http://rp5.kz/rss/76334/ru')
                # читаем погоду
                subprocess.call(["mplayer", "http://translate.google.com/translate_tts?tl=ru&ie=UTF-8&q="+d.entries[0].title])

def destroy():
        # заканчиваем работу, выключаем свет )
    GPIO.digitalWrite(LIGHT, GPIO.LOW)
```

### Схема

![](posts/rpi-webiopi-1.png)

Транзистор любой NPN структуры, я использовал советский КТ961Б, для работы в режиме коммутации подойдет любой. Сопротивление подобрать для необходимого тока открывания транзистора. Реле я взял SRI-05VDC-SD-C с напряжением 5 вольт и максимальным током для контактов 10А. Диод 1N4001 вроде, необходим для убирания эффекта самоиндукции катушки реле, иначе попалим транзистор и ногу малинки. Земля на транзисторе общая с питанием реле и малиной.

<div class="alert alert-warning"><strong>Внимание!</strong> Ни в коем случае не питайте реле от GPIO! Спалите «малину».</div>

Перед питанием можно включить реле. А так желательно отдельный источник питания для таких штук. Реле можно и на 12 вольт. Просьба не винить меня за спаленные малины, делаете все на свой страх и риск. Необходимо уметь держать паяльник и знать основы работы с электрическими цепями. 

<div class="alert alert-warning"><strong>Внимание!</strong> Осторожнее в цепях 220 вольт! Это смертельно.</div>
