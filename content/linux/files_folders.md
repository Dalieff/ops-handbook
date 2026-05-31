+++
title = "Files & Folders"
tags = ["linux"]
date = "2026-02-15"
+++  

{{< notice note >}}
Работа с файлами и каталогами
{{< /notice >}}
{{< notice info >}} 
Создание
{{< /notice >}}
```bash
touch file.txt                  # создать файл
mkdir dir                       # создать каталог
mkdir -p a/b/c                  # создать вложенные каталоги
install -m 644 file.txt /path/  # создать файл с правами
```
{{< notice info >}}
Просмотр
{{< /notice >}}
```bash
cat file.txt            # вывести файл
less file.txt           # постраничный просмотр
more file.txt           # старый pager 'legacy'
head file.txt           # первые строки
head -n 20 file.txt     # первые 20 строк
tail file.txt           # последние строки
tail -f file.log        # live просмотр логов
stat file.txt           # информация о файле
```
{{< notice info >}}
Редактирование
{{< /notice >}}
```bash
nano file.txt           # простой редактор
vim file.txt            # продвинутый редактор* (см. vim)
vi file.txt             # классический vi
```
{{< notice info >}}
Копирование, перемещение, удаление
{{< /notice >}}
```bash
cp file1 file2          # копировать файл
cp -r dir1 dir2         # копировать каталог
cp -a dir1 dir2         # сохранить всё (права, время)
mv file1 file2          # переместить / переименовать
rm file.txt             # удалить файл
rm -r dir               # удалить каталог
rm -rf dir              # принудительно удалить (ОСТОРОЖНО)
rmdir empty_dir         # удалить пустой каталог
```
{{< notice info >}}
Links
{{< /notice >}}
```bash
ln file link        # hard link
ln -s file symlink  # символическая ссылка
```
{{< notice warning >}}
Примечание: Будь аккуратнее с комбинацией rm -rf, она удаляет все данные безвозвратно. Уничтожение системы
{{< /notice >}}


{{< notice note >}}
Ссыдки и примечания
{{< /notice >}}
*[vim]({{< relref "/linux/network.md" >}})*
*[nano]({{< relref "/linux/network.md" >}})*
