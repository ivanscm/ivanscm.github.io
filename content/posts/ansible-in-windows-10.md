---
title: "Запуск Ansible (и любых Linux программ и команд) в Windows 10"
date: 2020-04-25T20:14:51+07:00
draft: false
categories: ["technologies", "work", "linux", "tools"]
images: ["posts/ansible-in-windows-10.png"]
featured_image: "posts/ansible-in-windows-10.png"
---
Речь в заметке пойдет о запуске Linux приложений под Windows 10 с помощью <abbr title="Windows Subsystem for Linux - подсистема Windows для Linux">WSL</abbr> без виртуальной машины, средствами Windows.  

**Предыстория**

У нас, в компании Кузбасс-ЦОТ, есть программный продукт "<abbr title="Видеоинформационный комплекс массового развития и непрерывного контроля компетентности работников в сфере безопасности труда">Комплекс</abbr>". Это такая штука, которая обучает и тестирует людей, вычисляет их компетентность и прогнозирует риски. Сам продукт представляет собой сервер, написаный на языке PHP, и клиент на Qt5, работающий на вандалоустойчивом терминале с ОС Ubuntu. 

Есть проблема в следующем: при внедрении ПО на предприятии заказчика, количество этих терминалов может достигать десятки штук, а то и сотню. Приходится объезжать места физической установки терминалов и вручную накатывать образ через Clonezilla, далее конфигурировать его, указывать IP адрес сервера. Так как терминалы поставляются уже с предустановленной ОС, данную  проблему я решил исправить с помощью [Ansible](https://www.ansible.com/). Если просто, это такая система управления конфигурациями, где состояние конечной системы описывается в файлах формата YAML и далее это состояние поддерживается.
<!--more-->

### Описание решения
Хотя Ansible и написан на Python, установить его через pip, как рассказывается в [документации](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html?extIdCarryOver=true&sc_cid=701f2000001OH7YAAW#installing-ansible-with-pip), не получилось, так как требовались возможности Linux.

Ставить второй системой Linux или запускать его в виртуальной машине мне очень не хотелось. Поэтому я пошел по пути меньшего сопротивления и решил использовать WSL. Это подсистема Windows для Linux, позволяющая запускать много программ и утилит без виртуализации. Оговорюсь - это не полноценный Linux, а лишь только реализация окружения и утилит.

<div class="alert alert-warning" role="alert">
  <strong>Внимание!</strong> WSL работает только на 64 битных версиях Windows 10!
</div>

### Установка WSL
Для установки WSL требуется включить компонент **Подсистема Windows для Linux**. Откройте PowerShell с правами администратора и выполните команду, включающую данный компонент:

```ps1
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

Далее потребуется перезагрузить компьютер.

Теперь установите через Microsoft Store необходимый дистрибутив Linux. Полный список доступных дистрибутивов можно посмотреть по [ссылке](https://www.microsoft.com/ru-ru/search/shop/apps?q=wsl&category=Developer+tools). Для себя я выбрал [Ubuntu 18.04 LTS](https://www.microsoft.com/store/apps/9N9TNGVNDL3Q).

После установки и запуска, откроется терминал с приглашением указать имя пользователя и пароль для данного дистрибутива. Пароль можно не указывать, пустые пароли пропускает. WSL установлена!

### Установка Ansible
Далее я следовал [документации](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-ubuntu) по установке Ansible для Ubuntu:
```bash
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible
```

Ansible установлен! Теперь можно работать с ним из системы Windows 10:

```ps1
PS C:\Users\ivans> wsl ansible
usage: ansible [-h] [--version] [-v] [-b] [--become-method BECOME_METHOD]
               [--become-user BECOME_USER] [-K] [-i INVENTORY] [--list-hosts]
               [-l SUBSET] [-P POLL_INTERVAL] [-B SECONDS] [-o] [-t TREE] [-k]
               [--private-key PRIVATE_KEY_FILE] [-u REMOTE_USER]
               [-c CONNECTION] [-T TIMEOUT]
               [--ssh-common-args SSH_COMMON_ARGS]
               [--sftp-extra-args SFTP_EXTRA_ARGS]
               [--scp-extra-args SCP_EXTRA_ARGS]
               [--ssh-extra-args SSH_EXTRA_ARGS] [-C] [--syntax-check] [-D]
               [-e EXTRA_VARS] [--vault-id VAULT_IDS]
               [--ask-vault-pass | --vault-password-file VAULT_PASSWORD_FILES]
               [-f FORKS] [-M MODULE_PATH] [--playbook-dir BASEDIR]
               [-a MODULE_ARGS] [-m MODULE_NAME]
               pattern
ansible: error: too few arguments
```

Командой `wsl` можно вызывать команды WSL из PowerShell в требуемой директории не запуская приложение. Например так можно установить и запустить редактор `nano`:
```ps1
wsl sudo apt install nano
wsl nano
```

Думаю данная заметка пригодится вам, спасибо за прочтение. Буду рад, если поделитесь статьей с коллегами разработчиками.