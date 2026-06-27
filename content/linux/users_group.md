+++
title = "Users & Groups"
tags = ["linux"]
date = "2026-02-15"
+++  

### Управление пользователями и группами


{{< notice note >}}
Управление пользователями и группами
{{< /notice >}}
### Управление пользователями
{{< notice info >}}
Управление пользователями
{{< /notice >}}
```bash
whoami                  # кто ты сейчас
id                      # UID, GID, группы
id user                 # UID, GID и все группы пользователя
who                     # кто сейчас в системе
w                       # активные пользователи + процессы
users                   # список активных сессий пользователей (упрощённый), “bonus”
last                    # история входов пользователей
lastlog                 # last login per user (may be unavailable on some distros)
getent passwd user      # Проверка пользователя в системе
chsh -s /bin/bash       # сменить свой shell без root
id -gn                  # показать только имя основной группы
```
### Создание пользователей

{{< notice info >}}
Создание пользователей
{{< /notice >}}
```bash
adduser user                                # интерактивное создание (Debian/Ubuntu)
useradd user                                # низкоуровневое создание пользователя
useradd -m user                             # создать home директорию
useradd -m -s /bin/bash -G sudo user        # задать shell v.1
useradd -m -d /home/user -s /bin/bash user  # задать shell v.2
useradd -u 1500 user                        # задать UID вручную
useradd -g devs user                        # primary group (основная группа)
useradd -m -s /bin/bash -U user             # -U = создать одноимённую группу
```
{{< notice info >}}
Passwords
{{< /notice >}}
```bash
passwd                  # сменить свой пароль
passwd user             # сменить пароль пользователя
passwd -l user          # заблокировать пароль
passwd -u user          # разблокировать пароль
```
{{< notice info >}}
Редактирование пользователей
{{< /notice >}}
```bash
usermod -aG devs user               # добавить в дополнительную группу (recommended)
usermod -G devs,adm user            # overwrites all supplementary groups (be careful)
usermod -s /bin/zsh user            # сменить shell
usermod -d /home/newhome -m user    # переместить home, сменить home директорию
usermod -l newname user             # переименовать пользователя
usermod -u 2000 user                # изменить UID
usermod -L user                     # lock account
usermod -U user                     # unlock account
```
{{< notice info >}}
Remove users
{{< /notice >}}
```bash
userdel user                # удалить пользователя
userdel -r user             # удаляет home и mail spool, потенциально данные
```
{{< notice info >}}
Управление группами
{{< /notice >}}
```bash
groups                      # группы текущего пользователя
groups user                 # группы пользователя
groupadd devs               # создать группу
groupmod -n newdevs devs    # переименовать группу
groupdel devs               # удалить группу
gpasswd -a user devs        # добавить пользователя в группу. Это legacy tool, чаще используют usermod -aG
gpasswd -d user devs        # удалить пользователя из группы
```
{{< notice info >}}
Файлы учетных записей
{{< /notice >}}
```bash
cat /etc/passwd             # список пользователей
cat /etc/group              # список групп
cat /etc/shadow             # root-only, stores password hashes (root, sensitive)
getent passwd               # пользователи (системный источник)
getent group devs           # works with LDAP/NIS/system DB
```