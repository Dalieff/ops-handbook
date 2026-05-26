+++
title = "Handlers"
tags = ["ansible"]
date = "2026-02-15"
+++  

{{< notice info >}}
Классический кейс (Один триггер — один хэндлер)
Самый частый вариант: конфиг изменился -> хэндлер перезапустил сервис. Если конфиг не менялся, шаг с перезапуском просто пропускается.
{{< /notice >}}
```go
tasks:
  - name: Кейс 1 — Меняем конфиг Nginx
    copy:
      src: nginx.conf
      dest: /etc/nginx/nginx.conf
    notify: Перезапустить Nginx  # Имя должно строго совпадать с именем хэндлера

handlers:
  - name: Перезапустить Nginx
    systemd:
      name: nginx
      state: restarted
```
---
{{< notice info >}}
Один триггер вызывает цепочку хэндлеров (Список)
Если изменение одного файла требует совершить сразу несколько действий (например, сбросить кэш, а потом перезапустить сервис), в notify можно передать список.
{{< /notice >}}
```go
tasks:
  - name: Кейс 2 — Обновляем настройки приложения
    template:
      src: app.conf.j2
      dest: /var/www/app.conf
    notify:
      - Очистить кэш приложения
      - Перезапустить приложение

handlers:
  - name: Очистить кэш приложения
    file:
      path: /var/www/cache/
      state: absent

  - name: Перезапустить приложение
    systemd:
      name: myapp
      state: restarted
```
---
{{< notice info >}}
Несколько тасок вызывают один и тот же хэндлер
Если у тебя три разные таски меняют разные конфиги одного сервиса, они все могут вызывать один хэндлер. Ansible сообразит и выполнит его всего один раз в самом конце плейбука.
{{< /notice >}}
```go
tasks:
  - name: Кейс 3.1 — Настраиваем первый конфиг
    copy: src=site.conf dest=/etc/nginx/conf.d/
    notify: Перезапустить Nginx

  - name: Кейс 3.2 — Настраиваем второй конфиг
    copy: src=ssl.conf dest=/etc/nginx/conf.d/
    notify: Перезапустить Nginx

handlers:
  - name: Перезапустить Nginx
    systemd:
      name: nginx
      state: restarted
```
---
{{< notice info >}}
Группировка через темы (Параметр listen)
Вместо того чтобы прописывать длинные имена хэндлеров, им можно дать один общий «канал связи» через listen. Таски шлют уведомление в этот канал, и все подписанные хэндлеры срабатывают.
{{< /notice >}}
```go
tasks:
  - name: Кейс 4 — Меняем настройки веб-сервера
    copy: src=php.ini dest=/etc/php.ini
    notify: "web_config_changed"  # Шлем сигнал в канал

handlers:
  - name: Перезапуск PHP
    systemd: name=php-fpm state=restarted
    listen: "web_config_changed"   # Слушает канал

  - name: Перезапуск Nginx
    systemd: name=nginx state=restarted
    listen: "web_config_changed"   # Тоже слушает этот канал
```
---
{{< notice info >}}
Принудительный вызов хэндлеров прямо посреди плейбука (Flush Handlers)
По умолчанию Ansible копит все сигналы notify и выполняет хэндлеры в самом-самом конце плейбука. Но если тебе нужно, чтобы сервис перезапустился немедленно (например, потому что следующие таски будут делать к нему запросы), используется специальный модуль meta: flush_handlers.
{{< /notice >}}
```go
tasks:
  - name: Кейс 5.1 — Ставим и настраиваем базу данных
    template: src=my.cnf.j2 dest=/etc/mysql/my.cnf
    notify: Перезапустить MySQL

  - name: Кейс 5.2 — Форсируем выполнение хэндлера прямо сейчас
    meta: flush_handlers  # Ansible прервется и перезапустит MySQL прямо тут

  - name: Кейс 5.3 — Накатываем бэкап (база уже должна железно работать)
    mysql_db: name=prod state=import target=/tmp/backup.sql
```
---
{{< notice warning >}}
Хэндлеры внутри ролей
Когда ты начнешь раскладывать код по ролям, для хэндлеров создается отдельный файл внутри папки роли: roles/имя_роли/handlers/main.yml. Их синтаксис там точно такой же, но их больше не нужно объявлять в основном плейбуке — Ansible подтянет их автоматически.
{{< /notice >}}
---