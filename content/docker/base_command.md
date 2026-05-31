+++
title = "Base Command"
tags = ["docker"]
date = "2026-02-15"
+++

{{< notice note >}}
30–40 команд которые закрывают 90% работы с Docker. Остальное используется редко.
{{< /notice >}}

{{< notice info >}}
Информация
{{< /notice >}}
```yaml
docker version # версия
docker info  # Информация о системе
```
{{< notice info >}}
Работа с образами
{{< /notice >}}
```yaml
docker pull nginx # Скачать образ
docker pull nginx:17 # Скачать конкретную версию
docker images # Посмотреть образы v.1
docker image ls # Посмотреть образы v.2
docker rmi nginx # Удалить образ
docker rmi image1 image2 # Удалить несколько образов
docker build -t myapp . # Собрать образ
docker build -t myapp:v1 . # Собрать образ с тегом
docker tag myapp:v1 myrepo/myapp:v1 # Изменить тег
docker push myrepo/myapp:v1 # Загрузить образ в Registry
docker history myapp:v1 # Посмотреть слои образа (помогает понять, почему он весит 2 ГБ)
```
{{< notice info >}}
Работа с контейнерами
{{< /notice >}}
```yaml
docker run nginx # Запустить контейнер
docker run -d nginx # Запустить в фоне
docker run -d --name web nginx # Задать имя
docker run -d --name web -p 8080:80 nginx # Пробросить порт
docker run -e MYSQL_ROOT_PASSWORD=secret mysql # Передать переменную окружения
docker run -v pgdata:/var/lib/postgresql/data postgres # Подключить том
docker run --restart unless-stopped nginx # Автоперезапуск
docker run -it ubuntu bash # Запустить интерактивно
docker ps # Список контейнеров
docker ps -a # Список всех контейнеров
docker stop web # Остановить контейнер
docker start web # Запустить остановленный
docker restart web # Перезапустить
docker rm web # Удалить контейнер
docker rm -f web # Удалить работающий контейнер
docker run --rm -it ubuntu bash # Запустить временный контейнер (сам удалится после выхода)
docker ps -q # Вывести только ID контейнеров (удобно для скриптов и массового удаления)
```
{{< notice info >}}
Работа внутри контейнера
{{< /notice >}}
```yaml
docker exec web ls / # Выполнить команду
docker exec -it web bash # Войти в контейнер
docker exec -it web sh # Если bash нет
docker top web # Посмотреть процессы
```
{{< notice info >}}
Логи
{{< /notice >}}
```yaml
docker logs web # Просмотр логов
docker logs -f web # Смотреть в реальном времени
docker logs --tail 100 web # Последние 100 строк
docker logs --since 10m web # Посмотреть логи только за последние 10 минут
```
{{< notice info >}}
Копирование файлов
{{< /notice >}}
```yaml
docker cp web:/etc/nginx/nginx.conf . # Из контейнера
docker cp nginx.conf web:/etc/nginx/ # В контейнер
```
{{< notice info >}}
Сети Docker
{{< /notice >}}
```yaml
docker network ls # Список сетей
docker network create app_net # Создать сеть
docker network inspect app_net # Информация о сети
docker network connect app_net web # Подключить контейнер
docker network disconnect app_net web # Отключить контейнер
docker network rm app_net # Удалить сеть
```
{{< notice info >}}
Volumes
{{< /notice >}}
```yaml
docker volume ls # Список томов
docker volume create pgdata # Создать том
docker volume inspect pgdata # Информация о томе
docker volume rm pgdata # Удалить том
```
{{< notice info >}}
Мониторинг
{{< /notice >}}
```yaml
docker system df # Показать, сколько места на диске занимают образы, контейнеры и тома
docker stats # Использование ресурсов
docker stats web # Только один контейнер
docker inspect web # Информация о контейнере
docker inspect web | grep IPAddress # IP контейнера v.1
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' web # IP контейнера v.2
```
{{< notice info >}}
Docker Compose
{{< /notice >}}
```yaml
docker compose up # Запустить стек
docker compose up -d # В фоне
docker compose up -d --build # Пересобрать
docker compose down # Остановить
docker compose down -v # Остановить с удалением томов
docker compose ps # Посмотреть контейнеры
docker compose logs app # Логи
docker compose logs -f app # Смотреть логи
docker compose restart nginx # Перезапуск сервиса
docker compose exec -it nginx bash # Выполнить команду
docker compose config # Проверить синтаксис docker-compose.yml на ошибки без запуска
```
{{< notice info >}}
Очистка Docker
{{< /notice >}}
```yaml
docker container prune # Удалить остановленные контейнеры
docker image prune # Удалить неиспользуемые образы
docker system prune # Удалить всё неиспользуемое
docker system prune -a # Полная очистка
```
{{< notice info >}}
Ограничения (лимиты)
{{< /notice >}}
```yaml
docker run -d --name web -m 512m nginx # Ограничить оперативку (RAM) до 512 Мегабайт
docker run -d --name web --cpus="1.5" nginx # Ограничить использование процессора (1.5 ядра)
docker stats --no-stream # Показать загрузку памяти/ЦП один раз (не обновляя экран)
docker inspect web | grep -iE "memory|cpu" # Быстро проверить, какие лимиты заданы контейнеру
```
{{< notice info >}}
15 первоочередных команд, которые используются чаще всего
{{< /notice >}}
```yaml
docker ps
docker ps -a
docker images
docker pull
docker build
docker run
docker stop
docker start
docker restart
docker rm
docker logs -f
docker exec -it
docker inspect
docker compose up -d
docker compose down
```
{{< notice warning >}}
Уверенное владение этими командами, плюс понимание что такое контейнеры, образы, сети, тома и Compose, закрывает подавляющее большинство повседневных задач с Docker. Практически все вышеперечисленные команды выполняются в CLI — терминале Linux:
{{< /notice >}}
```yaml
Например:
• docker ps
• docker images
• docker logs -f nginx
• docker compose up -d
• docker exec -it nginx bash
Обычно схема работы DevOps выглядит так:
SSH → сервер → терминал → docker-команды
Например:
• ssh user@192.168.xxx.xxx
• docker ps
• docker logs -f nginx
• docker compose up -d
________________________________________
Есть и графические интерфейсы:
• Docker Desktop 
• Portainer 
• Rancher 
Но в реальной работе админа/DevOps основным инструментом остаётся терминал.
________________________________________
На собеседовании обычно ожидают, что ты умеешь через CLI:
1.	Запустить контейнер
2.	Посмотреть логи
3.	Зайти внутрь контейнера
4.	Проверить сеть
5.	Подключить volume
6.	Собрать образ из Dockerfile
7.	Запустить стек через Compose
8.	Найти проблему по docker ps, docker logs, docker inspect
То есть примерно 95% практических задач решаются именно через терминал
```