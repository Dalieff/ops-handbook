+++
title = "Must Have"
tags = ["linux"]
date = "2026-05-31"
+++

{{< notice note >}}
Работа с файлами и каталогами
{{< /notice >}}
{{< notice info >}}
Навигация и просмотр структуры
{{< /notice >}}
```bash
pwd         # текущая директория
ls -lh      # подробный список файлов с размером в читаемом виде
ls -a       # список всех файлов, включая скрытые
ls -R       # рекурсивный вывод каталогов
cd /path    # перейти в каталог
cd ..       # на уровень вверх
cd -        # назад в предыдущий каталог
tree        # дерево каталогов (если установлено)
# требует установки: fd-find / tree / ripgrep / fzf
```
{{< notice info >}}
Создание и редактирование
{{< /notice >}}
```bash
touch file.txt                  # создать пустой файл
mkdir -p a/b/c                  # создать вложенные каталоги
install -m 644 file.txt /path/  # скопировать/создать файл с заданными правами
nano file.txt                   # простой текстовый редактор
vim file.txt                    # продвинутый текстовый редактор
```
{{< notice info >}}
Вывод содержимого
{{< /notice >}}
```bash
cat file.txt        # вывести весь файл в терминал
less file.txt       # удобный постраничный просмотр
head -n 20 file.txt # первые 20 строк файла
tail -n 20 file.txt # последние 20 строк файла
tail -f file.log    # live просмотр логов в реальном времени
```
{{< notice info >}}
Копирование, перемещение, удаление
{{< /notice >}}
```bash
cp file1 file2      # копировать файл
cp -a dir1 dir2     # копировать каталог с сохранением прав и времени
mv file1 file2      # переместить или переименовать
rm file.txt         # удалить файл
rm -rf dir          # принудительно удалить каталог со всем содержимым (ОСТОРОЖНО)
rmdir empty_dir     # удалить пустой каталог
```
{{< notice info >}}
Ссылки и метаданные
{{< /notice >}}
```bash
ln file link        # создать жесткую ссылку
ln -s file symlink  # создать символическую ссылку
stat file.txt       # подробная информация о файле
```
{{< notice warning >}}
Примечание: Будь аккуратнее с комбинацией rm -rf, она удаляет все данные безвозвратно.
{{< /notice >}}
---
{{< notice note >}}
Управление пользователями, группами и правами
{{< /notice >}}
{{< notice info >}}
Информация о пользователях и сессиях
{{< /notice >}}
```bash
whoami      # имя текущего пользователя
id user     # UID, GID и группы конкретного пользователя
w           # активные пользователи и их процессы
last        # история входов в систему
```
{{< notice info >}}
Создание и модификация учетных записей
{{< /notice >}}
```bash
adduser user                                # интерактивное создание пользователя (Debian/Ubuntu)
useradd -m -s /bin/bash -G sudo,devs user   # создать пользователя с home, shell и добавить в группы
userdel -r user                             # удалить пользователя вместе с home-директорией
passwd user                                 # сменить пароль пользователю
passwd -l user                              # заблокировать пароль (заморозить аккаунт)
usermod -aG devs user                       # добавить пользователя в дополнительную группу
usermod -s /bin/zsh user                    # сменить shell пользователя
```
{{< notice info >}}
Управление группами
{{< /notice >}}
```bash
groups user           # группы пользователя
groupadd devs         # создать группу
groupdel devs         # удалить группу
gpasswd -d user devs  # удалить пользователя из группы
```
{{< notice info >}}
Управление правами доступа
{{< /notice >}}
```bash
chown user:group file.txt   # сменить владельца и группу файла
chmod 644 file.txt          # задать права чтения/записи (rw-r--r--)
chmod +x script.sh          # сделать файл исполняемым
umask 022                   # дефолтные права при создании (файлы 644 / папки 755)
```
{{< notice info >}}
Служебные файлы и повышение прав
{{< /notice >}}
```bash
cat /etc/passwd     # база данных пользователей
cat /etc/group      # база данных групп
getent passwd       # системный источник пользователей (включая сетевые)
sudo command        # выполнить команду от root
sudo -i             # интерактивный root shell
visudo              # безопасное редактирование файла /etc/sudoers
```
---
{{< notice note >}}
Поиск файлов, процессов и текста
{{< /notice >}}
{{< notice info >}}
Поиск в файловой системе
{{< /notice >}}
```bash
find /var -type f -name "*.log" -size +100M  # поиск файлов по маске и размеру
find . -mtime -1                             # файлы, измененные за последние сутки
find . -perm 644                             # поиск по правам доступа
fd nginx /etc                                # быстрый современный поиск файлов (замена find)
locate file.txt                              # мгновенный поиск по базе (требует updatedb)
```
{{< notice info >}}
Поиск текста внутри файлов
{{< /notice >}}
```bash
grep -rn -i "error" /var/log/  # рекурсивный поиск текста без учета регистра с номерами строк
grep -v "DEBUG" app.log        # исключить строки, содержащие "DEBUG"
rg -i "error" .                # ripgrep: максимально быстрый рекурсивный поиск
```
{{< notice info >}}
Поиск процессов и сетевых соединений
{{< /notice >}}
```bash
pgrep -l nginx          # найти PID процессов по имени
lsof -i :80             # показать, кто занял 80-й порт v.1
ss -tulpn | grep :80    # показать, кто занял 80-й порт v.2
lsof file.txt           # показать, какой процесс держит файл открытым
ss -ltnp                # список всех слушающих TCP-портов с именами процессов
```
{{< notice info >}}
Поиск команд и истории
{{< /notice >}}
```bash
which nginx             # путь к исполняемому файлу команды
type ls                 # показать тип команды (alias, встроенная или бинарник)
history | grep ssh      # поиск команд в истории терминала
```
{{< notice info >}}
Интерактивный поиск (fzf)
{{< /notice >}}
```bash
find . | fzf            # интерактивный выбор файла
history | fzf           # интерактивный поиск по истории команд
```
---
{{< notice note >}}
Диагностика системы и сервисов
{{< /notice >}}
{{< notice info >}}
Проверка прав и путей доступа
{{< /notice >}}
```bash
stat -c "%a %U %G %n" file.txt  # вывести права в октальном виде, юзера и группу
namei -l /path/to/file          # проверить права на всех уровнях вложенности каталогов
ls -ld /path/to/dir             # проверить права только на сам каталог
sudo -u user test -r file.txt   # проверить, может ли конкретный user читать файл
```
{{< notice info >}}
Systemd и логи сервисов
{{< /notice >}}
```bash
systemctl status nginx -l   # подробный статус сервиса
systemctl is-active nginx   # проверка, запущен ли сервис в данный момент
systemctl is-enabled nginx  # проверка, добавлен ли сервис в автозагрузку
systemctl restart nginx     # перезапуск сервиса
systemctl reload nginx      # перечит конфигурации без остановки. Не все сервисы поддерживают reload, зависит от unit-файла
journalctl -u nginx -f      # live-просмотр логов конкретного сервиса
journalctl -u nginx -xe     # вывод последних логов сервиса с разбором ошибок
journalctl -xe              # общие системные ошибки в реальном времени
dmesg -T | tail             # логи ядра с человеческим временем
```
{{< notice info >}}
Дисковая подсистема
{{< /notice >}}
```bash
df -h               # свободное место на дисках в понятном виде
df -i               # использование inode (кол-ва файлов) на файловых системах
du -sh * # оценка размера каталогов в текущей директории
lsblk               # древовидный список блочных устройств (диски, разделы)
mount | column -t   # табличный вывод смонтированных файловых систем
```
{{< notice info >}}
Списки контроля доступа (ACL)
{{< /notice >}}
```bash
apt install acl                 # Debian/Ubuntu. Иначе команды getfacl/setfacl могут не работать
getfacl file.txt                # посмотреть расширенные права доступа
setfacl -m u:user:rw file.txt   # дать конкретному пользователю права на файл
setfacl -b file.txt             # полностью удалить все ACL-права с файла
```


tar -czvf archive.tar.gz folder/     # создать tar.gz архив
tar -xzvf archive.tar.gz             # распаковать tar.gz

tar -cvf archive.tar folder/         # создать tar (без сжатия)
tar -xvf archive.tar                 # распаковка tar

gzip file.txt                        # сжать файл (заменит на file.txt.gz)
gunzip file.txt.gz                   # распаковать gzip

zip -r archive.zip folder/           # создать zip архив
unzip archive.zip                    # распаковать zip

xz file.txt                          # сильное сжатие (медленнее gzip)
unxz file.txt.xz                     # распаковка xz



ps aux                              # все процессы
ps aux --sort=-%mem                 # сортировка по памяти
ps aux --sort=-%cpu                 # сортировка по CPU

top                                 # базовый мониторинг процессов
htop                                # улучшенный top (нужна установка)

kill PID                            # завершить процесс
kill -9 PID                         # принудительное завершение

pkill nginx                         # убить процесс по имени
killall nginx                       # убить все процессы с именем

nice -n 10 command                  # запуск с низким приоритетом
renice 10 -p PID                    # изменить приоритет процесса


ip a                                # IP-адреса интерфейсов
ip r                                # таблица маршрутизации
ip link                             # сетевые интерфейсы

ping google.com                     # проверка доступности хоста
traceroute google.com               # маршрут до хоста (может требовать установки)

curl https://example.com            # HTTP-запрос
wget https://example.com/file       # скачать файл

ss -tulpn                           # открытые порты и процессы
netstat -tulpn                      # старый аналог ss (устаревший)

nslookup google.com                 # DNS-запрос
dig google.com                      # расширенный DNS-анализ (bind-tools/dnsutils)

scp file user@host:/path/           # копирование по SSH
ssh user@host                       # подключение к серверу