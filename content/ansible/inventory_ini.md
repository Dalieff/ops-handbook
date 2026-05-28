

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