+++
title = "Create Users & Groups"
tags = ["ansible"]
date = "2026-02-15"
+++  

{{< notice info >}}
Самый простой (один пользователь + одна группа)
Сначала создаем группу, а затем пользователя, привязывая его к этой группе как к основной (group).
{{< /notice >}}

```yaml
- name: Создаем группу разработчиков
  group:
    name: developers
    state: present

- name: Создаем пользователя и добавляем в основную группу
  user:
    name: devuser
    group: developers
    shell: /bin/bash
    state: present
```
---
{{< notice info >}}
Добавление в дополнительные группы (например, в docker или sudo)
Часто пользователя нужно оставить в его основной группе, но накинуть ему дополнительные права. Для этого используются параметры groups и обязательно append: yes (чтобы не затереть старые группы)
{{< /notice >}}
```yaml
- name: Добавляем пользователя в группы sudo и docker
  user:
    name: devuser
    groups: sudo,docker
    append: yes
```
---
{{< notice info >}}
Оптимальный (создание списком через цикл loop)
Чтобы не копировать код для каждого сотрудника, на практике используют циклы. Мы объявляем список пользователей и одной таской создаем их всех.
{{< /notice >}}
```yaml
- name: Создаем нескольких пользователей одним task
  user:
    name: "{{ item.name }}"
    groups: "{{ item.groups }}"
    append: yes
    state: present
  loop:
    - { name: 'alice', groups: 'developers,docker' }
    - { name: 'bob', groups: 'developers' }
    - { name: 'charlie', groups: 'sudo' }
```
---
{{< notice info >}}
Системные пользователи (для сервисов)
Когда ты ставишь Nginx, базу данных или кастомный скрипт, им нужен свой пользователь, но у него не должно быть возможности залогиниться в систему (у него нет домашней папки и командной оболочки).
{{< /notice >}}
```yaml
- name: Создаем системного пользователя для приложения
  user:
    name: myapp-user
    shell: /sbin/nologin
    system: yes
    create_home: no
```
---
{{< notice info >}}
На практике пользователям редко дают пароли — им сразу прописывают SSH-ключ для безопасного входа. Для этого используют дополнительный модуль ansible.posix.authorized_key.
{{< /notice >}}
```yaml
- name: Создаем пользователя devops
  user:
    name: devops
    shell: /bin/bash

- name: Добавляем ему публичный SSH-ключ для входа /*Для этого используют дополнительный модуль ansible.posix.authorized_key.*/
  authorized_key:
    user: devops
    state: present
    key: "ssh-rsa AAAAB3NzaC1yc2E... user@key"
```
---
{{< notice info >}}
Иногда нужно, чтобы Ansible не просто создал пользователя, но и сам сгенерировал для него пару SSH-ключей прямо внутри его домашней папки (например, чтобы этот user мог ходить на другие сервера)
{{< /notice >}}
```yaml
- name: Создаем пользователя и генерируем ему SSH-ключ
  user:
    name: build-agent
    generate_ssh_key: yes
    ssh_key_bits: 2048
    ssh_key_file: .ssh/id_rsa
```
---
{{< notice info >}}
Создание пользователя с хэшированным паролем
Если пароль всё же нужен (например, для локального входа или команд sudo), его нельзя писать в плейбук открытым текстом. Ansible требует хэш (обычно SHA-512)
{{< /notice >}}
```yaml
- name: Создаем пользователя с защищенным паролем
  user:
    name: adminuser
    # Пароль должен быть заранее захэширован (например, через mkpasswd)
    password: "$6$rounds=656000$mystring$encryptedpasswordhash..."
```
---
{{< notice warning >}}
Важно помнить: Если нужно удалить пользователя или группу, ты просто меняешь параметр state: present на state: absent
{{< /notice >}}