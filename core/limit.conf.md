limit.conf
==========


# /etc/security/limits.conf

+ Paste this (at the end of the file) to /etc/security/limits.conf (using nano /etc/security/limits.conf) and save it

```
nginx           soft    nofile          2097152
nginx           hard    nofile          2097152
www-data        soft    nofile          2097152
www-data        hard    nofile          2097152
```

+ Paste this (at the end of the file) to /etc/pam.d/common-session (using nano /etc/pam.d/common-session) and save it,

```
session required pam_limits.so
```
+ Change listen.backlog in /etc/php5/fpm/pool.d/www.conf

`listen.backlog = 65535`

+ Change workerrlimitnofile in /etc/nginx/nginx.conf 

`worker_rlimit_nofile 99999;`
