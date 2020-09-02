limits
======
### check by pid
    cat /proc/$(cat /var/run/mariadb/mariadb.pid)/limits | grep open.files

### check user limit
    su - mysql -c 'ulimit -a' -s '/bin/bash' | grep open

### service
    /usr/lib/systemd/system/mariadb.service
    LimitNOFILE=infinity
    LimitMEMLOCK=infinity

infinity = 65535

    systemctl daemon-reload
    systemctl restart mariadb
 
 ### sysctl
     vi /etc/sysctl.conf
     sysctl -p
 
 ### limits.conf
 
/etc/security/limits.conf

    mysql           soft    nofile          65535
    mysql           hard    nofile          65535

reboot