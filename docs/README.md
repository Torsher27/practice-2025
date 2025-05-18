# Безопасная конфигурация веб-сервера Apache на Ubuntu Linux

## Содержание
1. [Введение](#введение)
2. [Подготовка системы](#подготовка-системы)
3. [Базовая настройка безопасности](#базовая-настройка-безопасности)
4. [Защита виртуальных хостов](#защита-виртуальных-хостов)
5. [Настройка HTTPS](#настройка-https)
6. [Дополнительные меры защиты](#дополнительные-меры-защиты)
7. [Заключение](#заключение)

## Введение
В этом документе описывается процесс безопасной настройки веб-сервера Apache на операционной системе Ubuntu Linux. Руководство включает все необходимые этапы от установки до тонкой настройки параметров безопасности.

## Подготовка системы

Обновление пакетов и установка Apache.
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 -y
```

## Базовая настройка безопасности

Отключение ненужных модулей для уменьшения поверхности атак.
```bash
sudo a2dismod autoindex cgi cgid status
sudo systemctl restart apache2
```

Настройка файла /etc/apache2/apache2.conf.
```bash
ServerTokens Prod
ServerSignature Off
TraceEnable Off
FileETag None
Timeout 60
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 5
```

## Защита виртуальных хостов

Файл /etc/apache2/sites-available/000-default.conf.
```bash
<Directory /var/www/html>
    Options -Indexes -Includes -ExecCGI
    AllowOverride None
    Require all granted
</Directory>

<FilesMatch "^\.ht">
    Require all denied
</FilesMatch>
```

Ограничение HTTP-методов.
```bash
<Location "/">
    <LimitExcept GET POST HEAD>
        Require all denied
    </LimitExcept>
</Location>
```

## Настройка HTTPS

Установка Certbot.
```bash
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d example.com
```

Конфигурация SSL в файле /etc/apache2/mods-available/ssl.conf.
```bash
SSLProtocol TLSv1.2 TLSv1.3
SSLCipherSuite HIGH:!aNULL:!MD5
SSLHonorCipherOrder on
```

## Дополнительные меры защиты

Установка модулей безопасности.
```bash
sudo apt install libapache2-mod-evasive libapache2-mod-security2 -y
```

Настройка mod_evasive в файле /etc/apache2/mods-available/evasive.conf.
```bash
DOSHashTableSize 3097
DOSPageCount 2
DOSSiteCount 50
DOSPageInterval 1
DOSSiteInterval 1
DOSBlockingPeriod 60
```

## Заключение

Выполнив все описанные шаги, мы получим безопасно настроенный веб-сервер Apache. 
Также необходимо регулярно обновлять систему и периодически проверять безопасность с помощью инструментов вроде lynis.
