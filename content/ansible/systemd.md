+++
title = "Systemd"
tags = ["ansible"]
date = "2026-02-15"
+++  

{{< notice info >}}
Запуск и автозапуск сервиса
{{< /notice >}}
```go
- name: Кейс 1 — Запускаем сервис и включаем его в автозапуск
  systemd:
    name: nginx
    state: started
    enabled: yes
```
---
{{< notice info >}}
Остановка и отключение автозапуска
{{< /notice >}}
```go
- name: Кейс 2 — Полностью тушим сервис и убираем из автозапуска
  systemd:
    name: nginx
    state: stopped
    enabled: no
```
---
{{< notice info >}}
Перезапуск (Полный и мягкий)
{{< /notice >}}
```go
- name: Кейс 3.1 — Жесткий перезапуск (с остановкой процесса)
  systemd:
    name: nginx
    state: restarted

- name: Кейс 3.2 — Мягкий перезапуск (только перечитать конфиги приложения)
  systemd:
    name: nginx
    state: reloaded
```
---
{{< notice info >}}
Перезагрузка конфигурации самого systemd (Daemon-Reload)
Используется, если ты создал или изменил сам файл службы (например, /etc/systemd/system/myapp.service)
{{< /notice >}}
```go
- name: Кейс 4 — Заставляем systemd увидеть новые файлы юнитов на диске
  systemd:
    daemon_reload: yes
```
---
{{< notice info >}}
Полная блокировка сервиса (Mask)
Защищает от случайного запуска службы другими программами или вручную.
{{< /notice >}}
```go
- name: Кейс 5 — Маскируем сервис (запрет на любой запуск)
  systemd:
    name: apache2
    masked: yes
```
---
{{< notice info >}}
Управление таймерами и сокетами вместо сервисов
{{< /notice >}}
```go
- name: Кейс 6.1 — Запускаем фоновую задачу по таймеру systemd
  systemd:
    name: logrotate.timer
    state: started

- name: Кейс 6.2 — Запускаем сокет-активацию сервиса
  systemd:
    name: docker.socket
    state: started
```
---
{{< notice info >}}
Управление сервисами списком через Loop
{{< /notice >}}
```go
- name: Кейс 7 — Управляем сразу несколькими завязаными друг на друга службами
  systemd:
    name: "{{ item }}"
    state: started
    enabled: yes
  loop:
    - php8.3-fpm
    - nginx
    - redis-server
```
---
{{< notice info >}}
Сброс упавших сервисов (Reset Failed)
Если сервис упал с ошибкой, systemd помечает его статус как failed. Иногда, чтобы запустить его снова, нужно сначала сбросить этот флаг аварии
{{< /notice >}}
```go
- name: Кейс 8 — Сбрасываем статус "failed" для зависшей службы
  systemd:
    name: myapp
    state: reset-failed
```
---
{{< notice info >}}
Мягкий останов (Параметр no_block)
По умолчанию Ansible ждет, пока сервис полностью остановится или запустится, и только потом переходит к следующей таске. Если поставить no_block: yes, Ansible просто отдаст команду в systemd и сразу пойдет дальше, не дожидаясь завершения процесса. Это полезно для тяжелых сервисов, которые тушатся по несколько минут
{{< /notice >}}
```go
- name: Кейс 9 — Даем команду на остановку и сразу идем дальше
  systemd:
    name: heavy-database
    state: stopped
    no_block: yes
```
---
{{< notice info >}}
Управление пользовательскими сервисами (Параметры scope и user)
Иногда сервисы крутятся не на уровне всей системы (от root), а внутри сессии конкретного непривилегированного пользователя (например, systemctl --user)
{{< /notice >}}
```go
- name: Кейс 10 — Запускаем сервис внутри пространства пользователя
  systemd:
    name: user-script
    state: started
    scope: user
  become_user: devuser  # Переключаемся на нужного юзера
```
---
{{< notice info >}}
Принудительное включение автозапуска (Параметр force)
Если у тебя на сервере уже есть старые симлинки автозапуска для этого сервиса, которые конфликтуют с новыми, параметр force: yes заставит systemd перезаписать их заново
{{< /notice >}}
```go
- name: Кейс 11 — Жестко пересоздаем симлинки автозапуска
  systemd:
    name: custom-backend
    enabled: yes
    force: yes
```
---
>Service
---
{{< notice info >}}
Запуск и автозапуск
Проверяет, работает ли служба, и добавляет её в автозапуск при старте ОС
{{< /notice >}}
```go
- name: Кейс 1 — Стандартный запуск и автозапуск
  service:
    name: nginx
    state: started
    enabled: yes
```
---
{{< notice info >}}
Остановка и отключение автозапуска
{{< /notice >}}
```go
- name: Кейс 2 — Остановка и отключение автозапуска
  service:
    name: nginx
    state: stopped
    enabled: no
```
---
{{< notice info >}}
Полный перезапуск
Полностью останавливает процесс и поднимает его заново
{{< /notice >}}
```go
- name: Кейс 3 — Жесткий перезапуск сервиса
  service:
    name: nginx
    state: restarted
```
---
{{< notice info >}}
Мягкая перезагрузка (Reload)
Заставляет сервис перечитать конфигурационные файлы без остановки самого процесса
{{< /notice >}}
```go
- name: Кейс 4 — Перезагрузка конфигурации на лету
  service:
    name: nginx
    state: reloaded
```
---
{{< notice info >}}
Использование паттернов для имен (Параметр pattern)
Этот параметр нужен для старых систем инициализации (SysVinit). Если служба не умеет отдавать свой статус, мы можем подсказать Ansible, какую строку искать в списке запущенных процессов (ps), чтобы понять, работает сервис или нет
{{< /notice >}}
```go
- name: Кейс 5 — Запуск с проверкой имени процесса в системе
  service:
    name: custom-script
    state: started
    pattern: /usr/bin/custom-backend-process
```
---
{{< notice info >}}
Переопределение пути к менеджеру (Параметр use)
По умолчанию Ansible сам угадывает, чем управлять (systemd, init.d, upstart). Но ты можешь жестко заставить его использовать конкретный менеджер
{{< /notice >}}
```go
- name: Кейс 6 — Принудительно заставляем использовать старый sysvinit
  service:
    name: networking
    state: restarted
    use: sysvinit
```
---
{{< notice info >}}
Сон между действиями (Параметр sleep)
Если ты делаешь restarted, и сервису нужно время, чтобы корректно закрыть сетевые порты перед новым запуском, этот параметр делает паузу (в секундах) между остановкой и стартом.
{{< /notice >}}
```go
- name: Кейс 7 — Перезапуск с паузой в 5 секунд, чтобы порты успели освободиться
  service:
    name: tomcat
    state: restarted
    sleep: 5
```
---
{{< notice info >}}
Управление списком сервисов через Loop
{{< /notice >}}
```go
- name: Кейс 8 — Запуск пачки сервисов одним циклом
  service:
    name: "{{ item }}"
    state: started
  loop:
    - rsyslog
    - cron
    - ssh
```
---
{{< notice warning >}}
Модуль service — универсальный (абстрактный). Под капотом он сам определяет, какая система инициализации стоит на сервере (SysVinit, Upstart или systemd). Модуль systemd работает только с systemd, но зато открывает доступ к специфичным фичам: daemon_reload, masked, работа с таймерами (.timer) и сокетами (.socket). В современных проектах всегда лучше использовать конкретный модуль systemd
{{< /notice >}}