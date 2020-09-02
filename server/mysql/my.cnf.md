my.cnf
======

## mysql 8

/etc/my.cnf.d/mysql-server.cnf
```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
log-error=/var/log/mysql/mysqld.log
pid-file=/run/mysqld/mysqld.pid

tmpdir                      = /dev/shm

# MyISAM #
key_buffer_size                = 32M
sort_buffer_size               = 2M
read_rnd_buffer_size           = 1M
read_buffer_size               = 2M
join_buffer_size               = 2M
myisam_recover_options         = FORCE,BACKUP

# SAFETY #
max_allowed_packet             = 32M      # Проверить этот параметр
max_connect_errors             = 100
max_connections                = 400
max_user_connections           = 500

# CACHES AND LIMITS #
tmp_table_size                 = 512M
max_heap_table_size            = 512M    # Should be greater or equal to tmp_table_size
thread_cache_size              = 80
open_files_limit               = 65535    # Should be greater then table_open_cache
table_definition_cache         = 10000
table_open_cache               = 15000    # Проверить этот параметр. mysqltuner рекомендует >10 000
table_open_cache_instances     = 8
flush_time                     = 86400
interactive_timeout            = 1200
wait_timeout                   = 1800

# INNODB #
innodb_buffer_pool_size        = 16G
innodb_buffer_pool_instances   = 4
innodb_flush_method            = O_DIRECT
innodb_flush_log_at_trx_commit = 0
innodb_file_per_table          = 1
innodb_lock_wait_timeout       = 120
innodb_thread_concurrency      = 12
innodb_write_io_threads        = 16
innodb_read_io_threads         = 16
innodb_io_capacity             = 1000
innodb_log_file_size           = 512M
innodb_log_buffer_size         = 8M
innodb_stats_on_metadata = 0

skip-name-resolve
skip-innodb_doublewrite

#skip-log-bin
#sync_binlog = 0
```