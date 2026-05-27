+++
title = "Create Files & Folders"
tags = ["ansible"]
date = "2026-02-15"
+++  

{{< notice info >}}
Создание пустой папки (директории)
Чтобы создать папку, мы указываем state: directory. Если нужно создать цепочку папок (например, /app/src/config), Ansible сам создаст все недостающие родительские папки.
{{< /notice >}}
```yaml
- name: Создаем папку для приложения
  ansible.builtin.file:
    path: /var/www/myapp
    state: directory
    owner: www-data
    group: www-data
    mode: '0755'  # Права доступа (лучше писать в кавычках)
```
---
{{< notice info >}}
Создание абсолютно пустого файла (аналог команды touch)
Если файла нет, Ansible его создаст. Если он уже есть, модуль просто обновит время его модификации, не затирая содержимое.
{{< /notice >}}
```yaml
- name: Создаем пустой файл логов
  ansible.builtin.file:
    path: /var/log/custom_app.log
    state: touch
    owner: root
    group: root
    mode: '0644'
```
---
{{< notice info >}}
Создание символической ссылки (Symlink)
Очень частая задача при деплое — переключить ссылку на новую версию приложения. Нужен state: link, а также параметр src (на что указываем).
{{< /notice >}}
```yaml
- name: Создаем симлинк на конфиг
  ansible.builtin.file:
    src: /etc/nginx/sites-available/myapp.conf
    dest: /etc/nginx/sites-enabled/myapp.conf
    state: link
```
---
{{< notice info >}}
Изменение прав на уже существующий файл или папку
Если файл уже есть на сервере, и тебе нужно только поменять владельца или права, state можно вообще не писать.
{{< /notice >}}
```yaml
- name: Меняем владельца и права на папку
  ansible.builtin.file:
    path: /var/www/html
    owner: www-data
    group: www-data
    mode: '0775'
```
---
{{< notice info >}}
Рекурсивное изменение прав (для папки и всего её содержимого)
Если внутри папки много файлов и подпапок, и им всем нужно выставить одинаковые права, добавляется параметр recurse: yes.
{{< /notice >}}
```yaml
- name: Рекурсивно меняем права на всю директорию
  ansible.builtin.file:
    path: /opt/storage
    state: directory
    owner: admin
    recurse: yes
```
---
{{< notice info >}}
Если вдруг понадобится создание файлов или директорий через command
{{< /notice >}}
```yaml
- name: Создаем директорию через argv
  ansible.builtin.command:
    argv:
      - mkdir
      - /tmp/new_bash_dir
```
или 
через shell
```yaml
- name: Создаем директорию через shell
  ansible.builtin.shell:
    cmd: mkdir /tmp/new_bash_dir
```
---
{{< notice info >}}
Удаление файла или папки
Как мы уже разбирали, для очистки используется state: absent. Ему всё равно, файл это или огромная папка с поддиректориями — он удалит всё подчистую
{{< /notice >}}
```yaml
- name: Удаляем временную папку со всем содержимым
  ansible.builtin.file:
    path: /tmp/old_versions
    state: absent
```
---
{{< notice info >}}
Простой список (создание нескольких папок)
Если у всех папок должны быть одинаковые права и владелец, мы просто передаем список путей в loop. Вместо каждого элемента подставится переменная {{ item }}
{{< /notice >}}
```yaml
- name: Создаем структуру папок для проекта
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    owner: www-data
    group: www-data
    mode: '0755'
  loop:
    - /var/www/app
    - /var/www/app/uploads
    - /var/www/app/logs
    - /var/www/app/cache
```
---
{{< notice info >}}
Продвинутый список (словари с разными правами)
Если папки или файлы разные, и для каждого нужны свои права или разные владельцы, мы передаем в loop список словарей (ключ-значение). Тогда внутри таски мы обращаемся к конкретным свойствам: {{ item.path }}, {{ item.mode }} и т.д.
{{< /notice >}}
```yaml
- name: Создаем разные файлы с индивидуальными правами
  ansible.builtin.file:
    path: "{{ item.path }}"
    state: touch
    owner: "{{ item.owner }}"
    mode: "{{ item.mode }}"
  loop:
    - { path: '/tmp/public.txt', owner: 'nobody', mode: '0666' }
    - { path: '/root/secret.txt', owner: 'root', mode: '0600' }
    - { path: '/var/log/app.log', owner: 'www-data', mode: '0644' }
```
---
{{< notice info >}}
Смешанный деплой (создание папок и файлов одновременно)
Через loop можно даже комбинировать типы объектов, подставляя переменную в параметр state.
{{< /notice >}}
```yaml
- name: Настраиваем окружение (папки и файлы)
  ansible.builtin.file:
    path: "{{ item.path }}"
    state: "{{ item.type }}"
    owner: www-data
  loop:
    - { path: '/opt/myapp', type: 'directory' }
    - { path: '/opt/myapp/configs', type: 'directory' }
    - { path: '/opt/myapp/configs/production.env', type: 'touch' }

```
---
{{< notice warning >}}
Модуль ansible.builtin.file не умеет записывать текст внутрь файлов. Он управляет только самой структурой на диске (есть файл/нет файла, какие права).
Если тебе нужно создать файл и сразу записать в него текст, используются другие модули: ansible.builtin.copy (с параметром content), ansible.builtin.template (из шаблона) или ansible.builtin.lineinfile (дописать строку в существующий файл).
{{< /notice >}}