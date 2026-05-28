+++
title = "inventory.ini"
tags = ["ansible"]
date = "2026-02-15"
+++  

{{< notice note >}}
inventory.ini (Инвентарь и управление хостами). В этом файле описываются целевые серверы (хосты), объединяются в логические группы и задаются специфичные для них переменные подключения. Ниже пример боевого файла конфигурации
{{< /notice >}}

{{< notice info >}}
Одиночный хост вне групп
{{< /notice >}}
```ini
gateway.local ansible_host=192.168.1.254
```
{{< notice info >}}
Группы серверов по ролям
{{< /notice >}}
```ini
[webservers]
web-prod-01 ansible_host=185.22.44.10 ansible_user=deploy max_connections=100
web-prod-02 ansible_host=185.22.44.11 ansible_user=deploy max_connections=50
[dbservers]
db-prod-01 ansible_host=10.0.0.5 ansible_port=2222
```
{{< notice info >}}
Магия диапазонов (создаст хосты от worker-01 до worker-20)
{{< /notice >}}
```ini
[workers]
worker-[01:20].local
```
{{< notice info >}}
Локальный хост (для запуска тасок на своей машине без SSH)
{{< /notice >}}
```ini
localhost ansible_connection=local
```
{{< notice info >}}
 Мета-группа (объединение нескольких групп в одну)
{{< /notice >}}
```ini
[production:children]
webservers
dbservers
```
{{< notice info >}}
Переменные для всей группы серверов
{{< /notice >}}
```ini
[production:vars]
env=prod
ansible_connection=ssh
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_private_key_file=~/.ssh/id_ed25519
```
{{< notice info >}}
Проброс через джамп-сервер для закрытых подсетей
{{< /notice >}}
```ini
ansible_ssh_common_args='-o ProxyCommand="ssh -W %h:%p -q user@bastion.example.com"'
```