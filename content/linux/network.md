+++
title = "Network"
tags = ["linux"]
date = "2026-02-15"
+++ 

{{< notice note >}}
Linux Networking Cheat Sheet
{{< /notice >}}

{{< notice info >}}
Базовая диагностика
{{< /notice >}}
```bash
ip a                    # интерфейсы и IP-адреса
ip r                    # маршруты (default gateway)
ip link                 # состояние сетевых интерфейсов
ping 8.8.8.8            # проверка связи по IP
ping google.com         # проверка DNS + доступности
traceroute google.com   # маршрут до хоста (может требовать пакет)
mtr google.com          # улучшенный traceroute (live-анализ)
```
{{< notice info >}}
DNS
{{< /notice >}}
```bash
cat /etc/resolv.conf    # текущие DNS-серверы
nslookup google.com     # простой DNS lookup
dig google.com          # подробный DNS анализ
resolvectl status       # systemd-resolved (современные системы)
```
{{< notice info >}}
Ports and connections
{{< /notice >}}
```bash
ss -tulpn               # открытые порты + процессы
ss -tnap                # активные TCP соединения
lsof -i :80             # кто держит порт 80
netstat -tulpn          # старый вариант ss (deprecated)
```
{{< notice info >}}
Interfaces (RUNTIME)
{{< /notice >}}
```bash
ip link set eth0 up                     # включить интерфейс
ip link set eth0 down                   # выключить интерфейс
ip addr add 192.168.1.10/24 dev eth0    # добавить IP
ip addr del 192.168.1.10/24 dev eth0    # удалить IP
```
{{< notice info >}}
Firewall
{{< /notice >}}
```bash
ufw status              # показывает текущее состояние firewall и список правил
ufw allow 22/tcp        # разрешает входящие подключения на SSH (порт 22)
ufw deny 80/tcp         # блокирует HTTP трафик (порт 80)
ufw enable              # включает firewall и применяет правила
iptables -L -n -v       # показывает низкоуровневые правила firewall (старый механизм Linux)
nft list ruleset        # показывает правила nftables (современная замена iptables)
```
{{< notice info >}}
Маршрутизация
{{< /notice >}}
```bash
ip route show                           # таблица маршрутов
ip route add default via 192.168.1.1    # добавляет шлюз по умолчанию (default route)
ip route del default                    # удаляет шлюз по умолчанию (default route)
```
{{< notice info >}}
Основные конфигурационные файлы
{{< /notice >}}
```bash
/etc/hosts                              # локальное сопоставление доменов и IP (обходит DNS)
/etc/resolv.conf                        # настройки DNS-серверов для резолвинга доменов
/etc/hostname                           # имя хоста (hostname машины)
/etc/netplan/*.yaml                     # конфигурация сети в Ubuntu (IP, gateway, DNS)
/etc/sysconfig/network-scripts/         # сеть в RHEL/CentOS (старый способ настройки)
/etc/ssh/sshd_config                    # конфигурация SSH-сервера (доступ, порты, безопасность)
/etc/nsswitch.conf                      # порядок источников данных (DNS, files, LDAP и т.д.)
```
{{< notice info >}}
Быстрая диагностика
{{< /notice >}}
```bash
ip a                        # показывает сетевые интерфейсы и IP-адреса
ip r                        # показывает таблицу маршрутизации (куда идёт трафик)
ping gateway                # проверяет доступность локального шлюза (роутера)
ping 8.8.8.8                # проверяет интернет без участия DNS
ping google.com             # проверяет интернет + работоспособность DNS
ss -tulpn                   # показывает открытые порты и процессы, которые их слушают
cat /etc/resolv.conf        # показывает DNS-серверы системы
```