config
======
```
[poolname]

listen = /var/run/php-fpm/$pool.sock
;listen.backlog = -1

listen.owner = nginx 
listen.group = nginx
listen.mode = 0666

user = sftpuser
group = sftpuser

pm = static

pm.max_children = 10
pm.start_servers = 3
pm.process_idle_timeout = 30s
pm.min_spare_servers = 3
pm.max_spare_servers = 10
pm.max_requests = 0

;pm.status_path = /status
;ping.path = /ping

;request_terminate_timeout = 0
request_slowlog_timeout = 30s
slowlog = /var/log/php-fpm/$pool-slow.log
 
php_admin_value[sendmail_path] = /usr/sbin/sendmail -t -i -f no-reply@server.com
php_flag[display_errors] = on
php_admin_value[error_log] = /var/log/php-fpm/$pool-error.log
php_admin_flag[log_errors] = on
php_admin_value[memory_limit] = 128M

php_value[session.save_handler] = files
php_value[session.save_path]    = /var/lib/php/session/$pool
php_value[soap.wsdl_cache_dir]  = /var/lib/php/wsdlcache/$pool

;php_value[error_reporting] = 22519
```
```
[insecret_co]
user = user
group = user

listen = /var/run/php-fpm/php-fpm.sock
;listen.backlog = 2048

listen.owner = nginx
listen.group = nginx
listen.mode = 0660

pm = dynamic 
pm.max_children = 100
pm.start_servers = 50
pm.min_spare_servers = 20
pm.max_spare_servers = 50
pm.max_requests = 20000
;pm.status_path = /phpfpm-status

slowlog = /var/log/php-fpm/$pool-slow.log
request_slowlog_timeout = 300s

request_terminate_timeout = 360s

catch_workers_output = no

php_admin_value[error_log] = /var/log/php-fpm/$pool-error.log
php_admin_flag[log_errors] = on

php_value[session.save_handler] = files
php_value[session.save_path]    = /var/lib/php/session/$pool
php_value[soap.wsdl_cache_dir]  = /var/lib/php/wsdlcache/$pool
```