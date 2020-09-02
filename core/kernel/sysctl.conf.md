sysctl.conf
==============
**Do it on your own risk!**

```
vim /etc/sysctl.conf 
sysctl -p
```
https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt

https://easyengine.io/tutorials/linux/sysctl-conf/

https://networkengineer.me/2015/06/17/sysctl-etcsysctl-conf/

### IMPROVE SYSTEM MEMORY MANAGEMENT

```sh
# Increase size of file handles and inode cache
fs.file-max = 2097152

# Do less swapping
vm.swappiness = 10
vm.dirty_ratio = 60
vm.dirty_background_ratio = 2
```

### GENERAL NETWORK SECURITY OPTIONS

```sh
# Number of times SYNACKs for passive TCP connection
net.ipv4.tcp_synack_retries = 2

# Allowed local port range
net.ipv4.ip_local_port_range = 2000 65535

# Protect Against TCP Time-Wait
net.ipv4.tcp_rfc1337 = 1

# Decrease the time default value for tcp_fin_timeout connection
net.ipv4.tcp_fin_timeout = 15

### Decrease the time default value for connections to keep alive
net.ipv4.tcp_keepalive_time = 300
net.ipv4.tcp_keepalive_probes = 5
net.ipv4.tcp_keepalive_intvl = 15
```

### TUNING NETWORK PERFORMANCE

```sh
### Default Socket Receive Buffer
net.core.rmem_default = 31457280

### Maximum Socket Receive Buffer
net.core.rmem_max = 12582912

### Default Socket Send Buffer
net.core.wmem_default = 31457280

### Maximum Socket Send Buffer
net.core.wmem_max = 12582912

### Increase number of incoming connections
net.core.somaxconn = 65535

### Increase number of incoming connections backlog
net.core.netdev_max_backlog = 65535

### Increase the maximum amount of option memory buffers
net.core.optmem_max = 25165824

### Increase the maximum total buffer-space allocatable
### This is measured in units of pages (4096 bytes)
net.ipv4.tcp_mem = 65535 131072 262144
net.ipv4.udp_mem = 65535 131072 262144

### Increase the read-buffer space allocatable
net.ipv4.tcp_rmem = 8192 87380 16777216
net.ipv4.udp_rmem_min = 16384

### Increase the write-buffer-space allocatable
net.ipv4.tcp_wmem = 8192 65535 16777216
net.ipv4.udp_wmem_min = 16384

### Increase the tcp-time-wait buckets pool size to prevent simple DOS attacks
net.ipv4.tcp_max_tw_buckets = 1440000
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_tw_reuse = 1
```



### 2be reanalised:


```sh
tcp optim

ip route | while read p; do ip route change $p initcwnd 10 initrwnd 10; done

net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_window_scaling=1
net.ipv4.tcp_slow_start_after_idle=0

fs.file-max=500000

net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.lo.rp_filter = 1
net.ipv4.conf.eth0.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

net.core.somaxconn=16384

## Отключаем маршрутизацию TCP пакетов от источника

net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.lo.accept_source_route = 0
net.ipv4.conf.eth0.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0

##Рекомендуется увеличить размер backlog до 1000 или выше (для 10Gb линка можно поставить 30000)
net.core.netdev_max_backlog = 10000

net.ipv4.conf.all.accept_source_route = 0

##Переменная определяет максимальное время хранения SYN-запросов в памяти до момента получения третьего, завершающего установление соединения, пакета
net.ipv4.tcp_max_syn_backlog = 1024

###Максимальное число сокетов, находящихся в состоянии TIME-WAIT одновременно. При превышении этого порога «лишний» сокет разрушается и пишется сообщение в системный журнал. Цель этой переменной – предотвращение простейших разновидностей DoS-атак.

net.ipv4.tcp_max_tw_buckets = 720000
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_keepalive_time = 1800
net.ipv4.tcp_keepalive_probes = 7
net.ipv4.tcp_keepalive_intvl = 30

##Размер буферов по умолчанию для приема и отправки данных через сокеты
net.core.wmem_default = 4194394
net.core.rmem_default = 8388608

##Увеличиваем максимальный размер памяти отводимой для TCP буферов
net.core.wmem_max = 33554432
net.core.rmem_max = 33554432

##Тюнинг буферов для TCP и UDP соединений (min, default, max bytes)
net.ipv4.tcp_rmem = 4096 8388608 16777216
net.ipv4.tcp_wmem = 4096 4194394 16777216

##Отключаем перенаправление пакетов с одного сетевого интерфейса на другой.
net.ipv4.ip_forward = 0

##Фильтр обратного пути, защита от спуфинга (подмены адресов)
net.ipv4.conf.default.rp_filter = 1

## Controls whether core dumps will append the PID to the core filename
## Useful for debugging multi-threaded applications
kernel.core_uses_pid = 1

##Защита от TCP SYN Cookie
net.ipv4.tcp_syncookies = 1

##Controls the maximum size of a message, in bytes
kernel.msgmnb = 65536

##Controls the default maxmimum size of a mesage queue
kernel.msgmax = 65536

##Controls the maximum shared segment size, in bytes
kernel.shmmax = 68719476736

##Controls the maximum number of shared memory segments, in pages
kernel.shmall = 4294967296




#!/bin/bash

defrt=`ip route | grep "^default" | head -1`
ip route change $defrt initcwnd 10```


# Avoid a smurf attack
net.ipv4.icmp_echo_ignore_broadcasts = 1
# Turn on protection for bad icmp error messages
net.ipv4.icmp_ignore_bogus_error_responses = 1
# Turn on syncookies for SYN flood attack protection
net.ipv4.tcp_syncookies = 1
# Turn on and log spoofed, source routed, and redirect packets
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
# No source routed packets here
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
# Turn on reverse path filtering
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
# Make sure no one can alter the routing tables
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0
# Don't act as a router
net.ipv4.ip_forward = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
# Turn on execshild
kernel.exec-shield = 1
kernel.randomize_va_space = 1
