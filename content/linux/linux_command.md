+++
title = "Must Have Commands"
tags = ["linux"]
date = "2026-02-15"
+++  

stat -c "%a %n" file.txt    # права в числах
{{< notice note >}}
Управление пользователями и группами
{{< /notice >}}
{{< notice info >}}
Управление пользователями
{{< /notice >}}
```bash
who         # кто сейчас в системе
w           # активные пользователи + процессы
users       # список активных сессий пользователей (упрощённый), “bonus”
last        # история входов пользователей
lastlog     # последний вход каждого пользователя
```
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
useradd -g devs user                        # основная группа
```
{{< notice info >}}
Passwords
{{< /notice >}}
```bash
passwd              # сменить свой пароль
passwd user         # сменить пароль пользователя
passwd -l user      # заблокировать пароль
passwd -u user      # разблокировать пароль
```
{{< notice info >}}
Редактирование пользователей
{{< /notice >}}
```bash
usermod -aG devs user           # добавить в дополнительную группу (recommended)
usermod -G devs,adm user        # заменить список групп
usermod -s /bin/zsh user        # сменить shell
usermod -d /home/newhome user   # сменить home директорию
usermod -l newname user         # переименовать пользователя
usermod -u 2000 user            # изменить UID
```
{{< notice info >}}
Remove users
{{< /notice >}}
```bash
userdel user                    # удалить пользователя
userdel -r user                 # удалить пользователя + home
```
{{< notice info >}}
Управление группами
{{< /notice >}}
```bash
groups                          # группы текущего пользователя
groups user                     # группы пользователя
groupadd devs                   # создать группу
groupmod -n newdevs devs        # переименовать группу
groupdel devs                   # удалить группу
gpasswd -a user devs            # добавить пользователя в группу. Это legacy tool, чаще используют usermod -aG
gpasswd -d user devs            # удалить пользователя из группы
```
{{< notice info >}}
Файлы учетных записей
{{< /notice >}}
```bash
cat /etc/passwd             # список пользователей
cat /etc/group              # список групп
cat /etc/shadow             # хеши паролей (только root, sensitive)
getent passwd               # пользователи (системный источник)
getent group                # группы
```
{{< notice info >}}
Права на пользовательском уровне
{{< /notice >}}
```bash
chown user file.txt         # смена владельца
chown user:group file.txt   # владелец + группа
chgrp devs file.txt         # смена группы
chmod 644 file.txt          # права доступа
umask                       # дефолтные права создания файлов
umask 022                   # файлы 644 / папки 755
umask -S                    # human-readable
```
{{< notice info >}}
Root и повышение прав
{{< /notice >}}
```bash
sudo command                # выполнить от root
sudo -i                     # root shell
su -                        # переключиться на root
visudo                      # редактирование sudoers (ОЧЕНЬ ВАЖНО)
```
---
{{< notice note >}}
Диагностика
{{< /notice >}}
{{< notice info >}}
Проверка пользователя
{{< /notice >}}
```bash
whoami                  # кто ты сейчас
id                      # UID, GID, группы
groups                  # группы текущего пользователя
id user                 # группы конкретного пользователя
```
{{< notice info >}}
Проверка прав файла
{{< /notice >}}
```bash
ls -l file.txt                      # rwx права + владелец
stat file.txt                       # полная информация
stat -c "%a %U %G %n" file.txt      # права + user + group
namei -l /path/to/file              # проверка ВСЕГО пути (очень важно)
```
{{< notice info >}}
Проверка пути к файлу
{{< /notice >}}
```bash
ls -ld /path                        # права каталога
ls -ld /path/to                     # каждый уровень отдельно
                                    # 🔥 ключевой момент: x нужен (execute) на КАЖДОМ каталоге пути
```
{{< notice info >}}
Проверка доступа как 'System'
{{< /notice >}}
```bash
sudo -u user test -r file.txt          # может ли user читать файл
sudo -u user test -w file.txt          # может ли писать
sudo -u user test -x file.sh           # может ли выполнять
sudo -u user cat file.txt              # попытка доступа от другого пользователя
```
{{< notice info >}}
ACL (если есть расширенные права)
{{< /notice >}}
```bash
apt install acl                         # Debian/Ubuntu. Иначе команды getfacl/setfacl могут не работать
getfacl file.txt                        # ACL права
setfacl -m u:user:r file.txt            # добавить ACL
setfacl -m g:group:rx file.txt          # ACL группе
setfacl -b file.txt                     # убрать ACL
```
{{< notice info >}}
SUDO / ROOT ПРОВЕРКА
{{< /notice >}}
```bash
sudo -l                 # что разрешено через sudo
sudo -v                 # проверить доступ sudo
visudo                  # правила sudo (опасно редактировать)
sudo -l -U user         # проверка sudo прав пользователя 
```
{{< notice info >}}
SYSTEMD (SERVICE DEBUG)
{{< /notice >}}
```bash
systemctl status nginx -l        # подробный статус
systemctl is-active nginx        # работает ли сервис
systemctl is-enabled nginx       # автозапуск
systemctl restart nginx
systemctl reload nginx
journalctl -u nginx -f          # live логи сервиса
journalctl -u nginx -xe         # ошибки сервиса
```
{{< notice info >}}
Процессы и доступ
{{< /notice >}}
```bash
ps aux | grep process   # кто запустил процесс
lsof file.txt           # кто держит файл открытым
lsof -i :80             # кто занимает порт
ss -ltnp                # открытые listening порты
which command           # проверка прав выполнения команды
type command            # тоже
```
{{< notice info >}}
Проверка диска
{{< /notice >}}
```bash
df -h
df -i                 # inode usage (очень важно!)
du -sh *
lsblk
mount | column -t     # что куда смонтировано
```
{{< notice info >}}
Системные логи (часто причина отказа)
{{< /notice >}}
```bash
journalctl -xe          # ошибки доступа
dmesg | tail            # kernel-level ошибки
```
{{< notice warning >}}
Некоторые команды могут потребовать прав суперпользователя (sudo)
{{< /notice >}}
---
{{< notice note >}}
Поиск
{{< /notice >}}
{{< notice note >}}
Поиск файлов и каталогов
{{< /notice >}}
```bash
find . -name "file.txt"                 # поиск по имени
find . -type f -name "*.log"            # только файлы
find . -type d -name "nginx"            # только каталоги
find /var -size +100M                   # по размеру
find . -mtime -1                        # изменённые за 1 день
find . -perm 644                        # по правам
find . -user root                       # по владельцу
locate file.txt                         # быстрый поиск по базе, locate работает если установлен и проиндексирован
updatedb                                # обновить базу locate
fd "file.txt"                           # современная замена find
fd ".log"                               # поиск логов
fd nginx /etc                           # поиск в каталоге
```
{{< notice info >}}
Поиск внутри файлов
{{< /notice >}}
```bash
grep "text" file.txt                    # поиск строки
grep -r "text" .                        # рекурсивно
grep -i "text" file.txt                 # без регистра
grep -n "text" file.txt                 # с номерами строк
grep -l "text" *.log                    # показать только файлы
grep -v "text" file.txt                 # исключить совпадения
rg "text"                               # ripgrep (быстрее grep)
rg -i "error" .                         # рекурсивно без регистра
rg "main" src/                          # поиск в коде
ack "text"                              # альтернатива grep
```
{{< notice info >}}
Поиск команд и программ
{{< /notice >}}
```bash
which python                            # путь к бинарнику
whereis nginx                           # бинарник + docs
type ls                                 # тип команды
command -v python                       # POSIX вариант
```
{{< notice info >}}
Поиск процессов
{{< /notice >}}
```bash
ps aux | grep nginx                     # через ps
pgrep nginx                             # PID по имени
pidof nginx                             # PID программы
ps aux | grep nginx                     # в live режиме
htop                                    # интерактивный поиск
```
{{< notice info >}}
Поиск сетевых соединений и портов
{{< /notice >}}
```bash
ss -tulnp                               # открытые порты
ss -tulnp | grep 80                     # поиск порта
lsof -i :80                             # кто использует порт
lsof -u user                            # файлы пользователя
```
{{< notice info >}}
Поиск по системе и логам
{{< /notice >}}
```bash
journalctl -u nginx                     # логи сервиса
journalctl | grep error                 # поиск в логах
systemctl list-units | grep nginx       # поиск сервисов
dmesg | grep error                      # kernel логи
```
{{< notice info >}}
Поиск в истории и окружениии
{{< /notice >}}
```bash
history | grep ssh                      # поиск в истории
env | grep PATH                         # переменные окружения
```
{{< notice info >}}
Интерактивный поиск
{{< /notice >}}
```bash
fzf                                     # универсальный интерактивный поиск
find . | fzf                            # поиск файлов через интерфейс
history | fzf                           # поиск команд
ps aux | fzf                            # поиск процессов
```