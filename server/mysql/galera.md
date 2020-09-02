galera
======

Установка

На всех 3х нодах устанавливаем

    dnf -y install mariadb-server-galera

Правим файл на каждой ноде /etc/my.cnf.d/galera.cnf

```sh
[mysqld]

binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0

##
## WSREP options
##

wsrep_on=1
wsrep_provider=/usr/lib64/galera/libgalera_smm.so
#wsrep_provider_options=
wsrep_cluster_name="galera_cluster"
wsrep_cluster_address="gcomm://IP1,IP2,IP3"
wsrep_node_name="<NAME>"
wsrep_node_address="<IP ADRES>"
wsrep_slave_threads=1
wsrep_certify_nonPK=1
wsrep_max_ws_rows=0
wsrep_max_ws_size=2147483647
wsrep_debug=0
wsrep_convert_LOCK_to_trx=0
wsrep_retry_autocommit=1
wsrep_auto_increment_control=1
wsrep_drupal_282555_workaround=0
wsrep_causal_reads=0
wsrep_notify_cmd=

##
## WSREP State Transfer options
##

wsrep_sst_method=rsync
wsrep_sst_auth=root:

### In MariaDB 10.3 and later, Percona XtraBackup is not supported.
### This limitation is being tracked by Percona XtraBackup bug PXB-1550.
### However, it does not appear that there are plans to fix it.
###wsrep_sst_method=xtrabackup-v2
###wsrep_sst_auth=sstuser:passw0rd
###pxc_strict_mode=ENFORCING
```

На любой, только одной ноде, делаем инициализацию кластера

    galera_new_cluster

На остальных же нодах просто стартуем сервис 

    systemctl start mariadb.service

Проверяем что узлы добавились. 
На главной ноде сразупосле запуска команды galera_new_cluster происходят изменения в следующих файлах
/var/lib/mysql/gvwstate.dat

cat /var/lib/mysql/gvwstate.dat                                                                                                                                          
```sh
my_uuid: 0e076018-629f-11ea-8465-cf06425e066b
#vwbeg
view_id: 3 0e076018-629f-11ea-8465-cf06425e066b 3
bootstrap: 0
member: 0e076018-629f-11ea-8465-cf06425e066b 0 <---Появляются id нод
member: 5e8cca73-629f-11ea-ae75-33a0617593b4 0 <---Появляются id нод
member: 79a87466-629f-11ea-bbbb-065fda75f117 0 <---Появляются id нод
#vwend
```

```
/var/lib/mysql/grastate.dat

# GALERA saved state
version: 2.1
uuid:    0e0812ae-629f-11ea-9fd7-2b10f1453a27
seqno:   -1
safe_to_bootstrap: 1 
```
При запуске galera_new_cluster. После добовления 2й ноды меняется на "0"

Заходим в БД где смотрим кол-во нод в кластере…

```
[root@server mysql]# mysql -r

MariaDB [(none)]> show status like 'wsrep_cluster_size';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 3     |
+--------------------+-------+
1 row in set (0.002 sec)
```


### troubleshoot

После аварийного выключения всех нод, восстановление нужно начинать с той что выключилась последней

It may not be safe to bootstrap the cluster from this node. It was not the last one to leave the cluster and may not contain all the updates. To force cluster bootstrap with this node, edit the grastate.dat file manually and set safe_to_bootstrap to 1

<https://www.symmcom.com/docs/how-tos/databases/how-to-recover-mariadb-galera-cluster-after-partial-or-full-crash>

Если осталась одна нода, в автоматическом режиме:

    MariaDB [(none)]> set global wsrep_provider_options='pc.bootstrap=YES';
    
    
The cluster address shouldn't be empty like gcomm://. This should never be hardcoded into any configuration files. 

wsrep_provider_options
A useful option to set is pc.wait_prim=no to ensure the server will start running even if it can't determine a primary node. This is useful if all members go down at the same time.


### log
https://severalnines.com/database-blog/understanding-mysql-galera-cluster-and-mariadb-cluster-diagnostic-logse
