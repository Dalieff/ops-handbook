+++
title = "Inventory.ini & Ansible.cfg"
tags = ["ansible"]
date = "2026-02-15"
+++  

{{< notice note >}}
ansible.cfg - основной файл конфигурации системы управления конфигурациями Ansible. Задает глобальное и локальное поведение утилит Ansible, определяя параметры подключения к удаленным серверам, пути к файлам инвентаризации, модули по умолчанию и другие системные настройки.
Параметры указанные в ниже приведенном конфиге закроюб 95% задач в проекте и сделает деплой быстрым и предсказуемым
{{< /notice >}}

{{< notice info >}}
[default]
Путь к файлу со списком серверов (инвентарю)
{{< /notice >}}
```ini
inventory = ./inventory.ini
```
{{< notice info >}}
Путь к файлу который указывает Ansible, где автоматически взять пароль для расшифровки Vault
{{< /notice >}}
```ini
vault_password_file = ./.ansible_vault_pass
```
{{< notice info >}}
Путь к локальной папке с кастомными модулями
{{< /notice >}}
```ini
library = ./library
```
{{< notice info >}}
Путь, куда скачиваются сторонние коллекции из Ansible Galaxy
{{< /notice >}}
```ini
collections_path = ./collections
```
{{< notice info >}}
Путь к кастомным ролям проекта
{{< /notice >}}
```ini
roles_path = ./roles
```
{{< notice info >}}
Под каким пользователем подключаться к серверам по SSH
{{< /notice >}}
```ini
remote_user = root
```
{{< notice info >}}
Отключение проверки SSH-ключей (удобно, чтобы не жать "yes" при первом подключении)
{{< /notice >}}
```ini
host_key_checking = False
```
{{< notice info >}}
Сколько серверов настраивать одновременно (по дефолту 5, увеличиваем для скорости)
{{< /notice >}}
```ini
forks = 10
```
{{< notice info >}}
Отключает создание раздражающих файлов .retry при падении плейбука
{{< /notice >}}
```ini
retry_files_enabled = False
```
{{< notice info >}}
Выводит плейбук в красивом и читаемом JSON/YAML формате (вместо одной строки)
{{< /notice >}}
```ini
stdout_callback = yaml
```
{{< notice info >}}
[privilege_escalation]
Автоматически повышать права до root на целевых серверах
{{< /notice >}}
```ini
become = True
```
{{< notice info >}}
Способ повышения прав (обычно sudo)
{{< /notice >}}
```ini
become_method = sudo
```
{{< notice info >}}
Пользователь, в которого превращаемся (root)
{{< /notice >}}
```ini
become_user = root
```
{{< notice info >}}
Запрашивать ли пароль для sudo (False, если настроен sudoers без пароля)
{{< /notice >}}
```ini
become_ask_pass = False
```
{{< notice info >}}
[ssh_connection]
Включение пайплайнинга (задачи выполняются внутри одной SSH-сессии, это ускоряет работу в 2-3 раза)
{{< /notice >}}
```ini
pipelining = True
```
{{< notice info >}}
Настройки SSH-клиента: переиспользование соединений, чтобы не тратить время на хендшейк
{{< /notice >}}
```ini
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
```
{{< notice warning >}}
Секреты, пароли в ansible.cfg никогда не хранятся. Для шифрования секретов используется инструмент Ansible Vault, но сами зашифрованные данные лежат в других местах, которые подключаются к плейбуку.
Сам файл .ansible_vault_pass, обязательно добавляется в .gitignore
{{< /notice >}}




{{< notice info >}}
inventory.ini (Инвентарь и управление хостами)
В этом файле описываются целевые серверы (хосты), объединяются в логические группы и задаются специфичные для них переменные подключения.
Пример боевого файла конфигурации ниже
{{< /notice >}}

```ini
# 1. Одиночный хост вне групп
gateway.local ansible_host=192.168.1.254

# 2. Группы серверов по ролям
[webservers]
web-prod-01 ansible_host=185.22.44.10 ansible_user=deploy max_connections=100
web-prod-02 ansible_host=185.22.44.11 ansible_user=deploy max_connections=50

[dbservers]
db-prod-01 ansible_host=10.0.0.5 ansible_port=2222

# 3. Магия диапазонов (создаст хосты от worker-01 до worker-20)
[workers]
worker-.local

# 4. Локальный хост (для запуска тасок на своей машине без SSH)
localhost ansible_connection=local

# 5. Мета-группа (объединение нескольких групп в одну)
[production:children]
webservers
dbservers

# 6. Переменные для всей группы серверов
[production:vars]
env=prod
ansible_connection=ssh
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_private_key_file=~/.ssh/id_ed25519
# Проброс через джамп-сервер для закрытых подсетей
ansible_ssh_common_args='-o ProxyCommand="ssh -W %h:%p -q user@bastion.example.com"'
```

{{< notice info >}}
Основные параметры и правила:
* [имя_группы] — группирует серверы. По этому имени вызываются таски в плейбуках (hosts: webservers)
* web-prod-01 — понятный алиас хоста для логов, вместо безликого IP.
* ansible_host — реальный IP или домен сервера.
* ansible_user / ansible_port — переопределяют дефолтного юзера и SSH-порт для конкретной машины.
* ansible_python_interpreter — жестко задает путь к Python на целевом сервере.
* Кастомные переменные (например, max_connections) — можно объявлять прямо в строке хоста для использования в шаблонах и конфигах приложения
{{< /notice >}}