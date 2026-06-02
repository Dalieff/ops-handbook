+++
title = "Network"
tags = ["ansible"]
date = "2026-02-15"
+++  
{{< notice note >}}
Декларативный плейбук (Через файлы конфигурации). Этот метод описывает конечное состояние файлов. Ansible просто берет готовые конфиги (для сети ОС, для файрвола и для Docker-композиции), закидывает их на сервер, а службы хоста сами их читают.
{{< /notice >}}
```yaml
- name: Declarative Network Configuration (File-Based)
  hosts: all
  become: true
  gather_facts: true

  tasks:
    - name: 1. Network | Copy Netplan configuration file from template
      ansible.builtin.template:
        src: netplan.yaml.j2
        dest: /etc/netplan/01-netcfg.yaml
        mode: '0600'
      notify: Apply netplan

    - name: 2. Firewall | Copy UFW/NFTables rules file
      ansible.builtin.copy:
        src: files/rules.v4
        dest: /etc/iptables/rules.v4
        mode: '0644'
      notify: Reload iptables

    - name: 3. Docker | Copy Docker Compose file for microservices
      ansible.builtin.template:
        src: docker-compose.yml.j2
        dest: /opt/app/docker-compose.yml
        mode: '0644'
      notify: Restart docker compose

  handlers:
    - name: Apply netplan
      ansible.builtin.command: netplan apply

    - name: Reload iptables
      ansible.builtin.command: iptables-restore < /etc/iptables/rules.v4

    - name: Restart docker compose
      ansible.builtin.command: docker compose -f /opt/app/docker-compose.yml up -d --remove-orphans
```
{{< notice note >}}
Императивный плейбук (Напрямую через API и утилиты). Этот метод не создает никаких файлов конфигурации на диске. Ansible сам выполняет команды, стучится в системные службы и Docker API, настраивая сеть «на лету».
{{< /notice >}}
```yaml
- name: Imperative Network Configuration (API and Command-Based)
  hosts: all
  become: true
  gather_facts: true

  tasks:
    - name: 1. Network | Set IP address via NMCLI tool immediately
      community.general.nmcli:
        conn_name: "{{ ansible_default_ipv4.interface }}"
        ifname: "{{ ansible_default_ipv4.interface }}"
        type: ethernet
        ip4: "192.168.1.50/24"
        gw4: "192.168.1.1"
        state: present

    - name: 2. Firewall | Add rules directly into Linux Kernel table
      ansible.builtin.iptables:
        chain: INPUT
        protocol: tcp
        destination_port: "{{ item }}"
        jump: ACCEPT
      loop: ['22', '80', '443']

    - name: 3. Docker | Request Docker Engine API to create network bridge
      community.docker.docker_network:
        name: proxy_network
        driver: bridge
        ipam_config:
          - subnet: "172.30.0.0/16"

    - name: 4. Docker | Request Docker Engine API to run container
      community.docker.docker_container:
        name: web_server
        image: nginx:alpine
        networks:
          - name: proxy_network
        ports:
          - "80:80"
```