+++
title = "Dockerfile"
tags = ["docker"]
date = "2026-05-31"
+++  

{{< notice note >}}
Основные понятия и инструкции
Dockerfile — текстовый файл с инструкциями для сборки Docker-образов. Каждая команда создает новый неизменяемый слой (Layer). Docker кэширует слои: если файлы/команды не менялись, берется готовый кэш.
{{< /notice >}}
{{< notice info >}}
Сборка аргументов и Базовый образ
{{< /notice >}}
```dockerfile
# ARG: Переменные ТОЛЬКО на этапе сборки (в сам контейнер не попадают)
ARG VERSION=24.04
# FROM: Базовый образ (всегда первая рабочая строка Dockerfile)
FROM ubuntu:${VERSION}      # FROM: Базовый образ (всегда первая рабочая строка Dockerfile)
```
{{< notice info >}}
Окружение и Конфигурация
{{< /notice >}}
```dockerfile
# ENV: Переменные окружения (доступны и при сборке, и внутри запущенного контейнера)
ENV APP_HOME=/app DEBIAN_FRONTEND=noninteractive
# WORKDIR: Рабочая директория (как "cd", создается автоматически, если её нет)
WORKDIR $APP_HOME
```
{{< notice info >}}
Копирование файлов и Сборка
{{< /notice >}}
Копирование файлов и Сборка
```dockerfile
# COPY: Копирует локальные файлы/папки с хоста в контейнер (рекомендуется)
COPY src/ .
# ADD: То же, что COPY, но умеет качать файлы по URL и распаковывать tar-архивы
ADD https://example.com/file.tar.gz .
# RUN: Выполняет команды при СБОРКЕ (создает новый слой). Разделяй через && для уменьшения слоев
RUN apt-get update && apt-get install -y --no-install-recommends curl ca-certificates && rm -rf /var/lib/apt/lists/*
```
{{< notice info >}}
Порты, Диски и Безопасность
{{< /notice >}}
```dockerfile
# EXPOSE: Документирует порт, который контейнер слушает (чисто информативно)
EXPOSE 8080
# VOLUME: Размечает точку монтирования для постоянных данных
VOLUME ["/data"]
# USER: Меняет пользователя для безопасности (чтобы не запускать процессы от root)
USER node
```
{{< notice info >}}
Проверки и Сигналы
{{< /notice >}}
```dockerfile
# HEALTHCHECK: Проверяет живой ли сервис внутри контейнера
HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost:8080/ || exit 1
# STOPSIGNAL: Сигнал для корректной остановки контейнера (дефолт SIGTERM)
STOPSIGNAL SIGTERM
# ONBUILD: Триггер. Сработает, только если этот образ станет базовым для другого Dockerfile
ONBUILD RUN echo "Этот шаг выполнится в дочернем образе!"
```
{{< notice info >}}
Запуск контейнера (Разница форматов)
{{< /notice >}}
```dockerfile
# EXEC-форма (Рекомендуется): Процесс идет как PID 1, корректно ловит сигналы (SIGTERM)
# SHELL-форма (Не рекомендуется): CMD python3 main.py (оборачивает в /bin/sh -c, не ловит сигналы)
# ENTRYPOINT: Постоянная точка входа. Делает контейнер похожим на исполняемый файл
ENTRYPOINT ["python3"]
# CMD: Аргументы по умолчанию для ENTRYPOINT. Легко переопределяются при "docker run"
CMD ["main.py"]
```
{{< notice note >}}
Шпаргалка по разнице команд
{{< /notice >}}
```dokerfile
RUN        -> Выполняется ОДИН раз в момент СБОРКИ образа.
CMD        -> Выполняется КАЖДЫЙ РАЗ при СТАРТЕ контейнера (дефолтная команда).
ENTRYPOINT -> Жесткая команда СТАРТА, которую нельзя случайно перебить аргументами.
```
{{< notice info >}}
ПРОДВИНУТАЯ КОНЦЕПЦИЯ: МНОГОЭТАПНАЯ СБОРКА (MULTI-STAGE BUILDS)
Позволяет компилировать код в тяжелом образе, а результат забирать в легкий.
{{< /notice >}}
```dockerfile
# ЭТАП 1: Сборка приложения (builder) ---
FROM maven:3.9 AS builder
WORKDIR /build
COPY . .
RUN mvn clean package
# ЭТАП 2: Финальный минимальный образ ---
FROM alpine:3.20
WORKDIR /app
# Копируем готовый артефакт из первого этапа. Весь мусор компилятора остается за бортом.
COPY --from=builder /build/target/app.jar ./app.jar
CMD ["java", "-jar", "app.jar"]
```
{{< notice note >}}
Примир стандартного dockerfile
{{< /notice >}}
```dockerfile
# 1. Базовый образ (всегда фиксируем версию)
FROM node:20.11-alpine
# 2. Создаем переменную для удобства
ENV NODE_ENV=production
# 3. Задаем рабочую директорию
WORKDIR /usr/src/app
# 4. Сначала копируем ТОЛЬКО файлы зависимостей (чтобы кэшировать их установку)
COPY package*.json ./
# 5. Устанавливаем только prod-зависимости и чистим кэш менеджера
RUN npm ci --only=production && npm cache clean --force
# 6. Копируем остальные исходники приложения
COPY . .
# 7. Переключаемся на безопасного пользователя (не root)
USER node
# 8. Документируем порт
EXPOSE 3000
# 9. Команда на запуск в exec-формате
CMD ["node", "src/index.js"]
```