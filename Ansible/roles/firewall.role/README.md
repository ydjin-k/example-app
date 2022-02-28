Firewall role
=============



USAGE
=====

На свой страх и риск меняем параметры ядра.

sysctl_rules:
  - name: "net.ipv4.conf.all.rp_filter"
    value: 1
  - name: "net.ipv4.tcp_syncookies"
    value: 1
  - name: "net.ipv4.tcp_max_syn_backlog"
    value: 2048
  - name: "net.ipv4.tcp_synack_retries"
    value: 1
  - name: "net.core.somaxconn"
    value: 2048
  - name: "net.ipv4.tcp_fin_timeout"
    value: 30
  - name: "net.ipv4.tcp_tw_recycle"
    value: 1
  - name: "net.ipv4.tcp_keepalive_intvl"
    value: 10
  - name: "net.ipv4.tcp_keepalive_probes"
    value: 5
  - name: "net.ipv4.tcp_keepalive_time"
    value: 60
  - name: "net.ipv4.conf.default.rp_filter"
    value: 1
  - name: "net.ipv4.ip_nonlocal_bind"
    value: 1


Доступен tag  iptables_conf iptables_syslog

Роль:
  - Добавляет правила в конфигурационный файл. По умолчанию добавляются разрешенные хосты,сети и порты.
  - Отключает ufw.
  - Создает файл логов, настраивает Rsyslog на выгрузку в лог (префикс "Firewall: ").
  - Настраивает ротацию по размеру.
  - Логируются хосты, которые пытаются сканировать или отправляют много icmp пакетов.

Умеет сопротивляться:
  - Сканированию портов (включая хитрые nmap-вские). Отслеживается обращения к закрытым портам и если они превышают допустимую норму, то соединение дропается.
  - Затоплению ping (если пакеты приходят больше чем 1 в 1 сек., то лишнее отбрасывается).
  - Отбрасывается различные кривые пакеты (фраг, кривые флаги тд).
  - SynFlood.
  - Блочит на внешнем интерфейсе немаршрутизируемые подсети.
  - Можно задавать количество одновременных соединений с одного хоста ( БЫТЬ ВНИМАТЕЛЬНЕЕ !!! ) по умолчанию выставил 80. Используется на случай если смогут пробиться через prerouting syn flood пакеты.

В таблице filter цепочка FORWARD выставлен в DROP!

```
firewall_ip_acl: []
#  - "1.1.1.1"
#  - "2.2.2.2"
#  - "3.3.3.3"

firewall_net_acl: []
#  - "4.4.4.4/12"
#  - "5.5.5.5/28"

firewall_main_ip_acl:
  - "188.93.16.2"       # ЦВТ-1
  - "188.93.16.50"      # ЦВТ-2
  - "95.213.254.17"     # OpenVPN
  - "95.213.142.253"    #Zabbix server (Managed)

######Включаем защиту от синфлуда ping scan. По умолчанию установлено в true
firewall_synflood_ping_scan_protect: true

firewall_external_network_interface: "eth0"

###Количество одновременно созданных соединений с одного хоста. Если клиент жалуется на работу сети, то лучше сразу обратить внимание сюда. Возможно стоит добавить в лог файл такие соединения.
establ_connect_from_host: 80

#####Количество пакетов в секунду с одного ip. По умолчанию установлен 60/s
limit_per_sec: 60

######Труба с которой все течет  по умолчанию в 20 установлено
limit_burst: 20

firewall_enabled_input_tcp_ports:
  - "80"
  - "443"
  - "22"

firewall_enabled_input_udp_ports: []

firewall_ip_blacklist: []

firewall_net_blacklist: []


```

