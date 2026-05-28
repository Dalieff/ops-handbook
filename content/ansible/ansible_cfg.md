+++
title = "ansible.cfg"
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
