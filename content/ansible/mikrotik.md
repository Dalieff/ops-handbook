+++
title = "mikrotik"
tags = ["ansible"]
date = "2026-02-15"
+++  

{{< notice info >}}
Для управления устройствами MikroTik через Ansible обычно требуется следующее:
* SSH-доступ к MikroTik (в RouterOS должен быть включён SSH)
* Коллекция Ansible для MikroTik - community.routeros documentation - Установка: ansible-galaxy collection install community.routeros
* Инвентарь (inventory.ini или inventory.yml) с описанием устройств (переменные подключения): IP-адрес устройства, логин, пароль или SSH-ключ
* Для большинства модулей RouterOS - ansible_connection=network_cli, ansible_network_os=community.routeros.routeros 
* При необходимости - Python-пакет paramiko (если используется соответствующий транспорт): pip install paramiko

Пример управления устройствами Mikrotik черз ansible.netcommon.cli_command`
{{< /notice >}}
```yaml
- name: Настройка Mikrotik RouterOS
  hosts: mikrotiks
  gather_facts: no

  tasks:
    - name: Посмотреть аптайм и ресурсы
      ansible.netcommon.cli_command:
        command: "/system resource print"
      register: router_info

    - name: Вывод информации
      debug:
        var: router_info.stdout_lines

    - name: Создаем бэкап конфигурации
      ansible.netcommon.cli_command:
        command: "/system backup save name=ansible_backup"
      register: backup_result

    - name: Add New User
      ansible.netcommon.cli_command:
        command: "/user add name=new_admin group=full password=password123"
      register: user_result

    - name: Remove User
      ansible.netcommon.cli_command:
        command: "/user remove new_admin"
      register: user_remove_result

и так далее по аналогии
```