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

{{< notice note >}}
Сейчас основным и рекомендуемым стандартом стал формат YAML (с расширением .yml или .yaml). INI-файлы всё ещё поддерживаются для обратной совместимости, и многие их используют по привычке, но сам Ansible уже давно продвигает именно YAML. Единый язык - и плейбуки, и переменные, и инвентарь теперь пишутся в одном стиле. Сложные структуры: В YAML намного проще и нагляднее описывать вложенные группы, списки и сложные конфигурации переменных. Вместо разделов в квадратных скобках [group] из INI-формата, в YAML структура строится за счет дерева отступов (иерархии). И вместо знака = для указания параметров хостов используются двоеточия : с обязательным пробелом после них. Отступы — строго по 2 пробела.
{{< /notice >}}

```yaml
Инвентарь, переписанный в современном формате YAML
all:
  children:
    frontend:
      hosts:
        nginx_vm:
          ansible_host: 192.168.12.101
    backend:
      hosts:
        apache_vm:
          ansible_host: 192.168.12.102
    database:
      hosts:
        postgresql_vm:
          ansible_host: 192.168.12.103

Хост, который не входит ни в одну группу - прямо в all.hosts
all:
  hosts:
    standalone_vm:
      ansible_host: 192.168.12.100
  children:
    frontend:
      hosts:
        nginx_vm:
          ansible_host: 192.168.12.101

Несколлько хостов в одной группе перечисляются внутри секции hosts этой группы.
all:
  children:
    frontend:
      hosts:
        nginx_vm_1:
          ansible_host: 192.168.12.101
        nginx_vm_2:
          ansible_host: 192.168.12.111
    backend:
      hosts:
        apache_vm_1:
          ansible_host: 192.168.12.102
        apache_vm_2:
          ansible_host: 192.168.12.112
```