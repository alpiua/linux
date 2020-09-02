snippets
========
# Import / export

**import database**

    $ mysql -u USER -p DATABASE < db_name.sql

**import from archive**

    gunzip < db_name.sql.gz | mysql -u USER -pPASSWORD DATABASE

**export database**

    $ mysqldump -u USER -p DATABASE > db_name.sql

**export all databases**

    $ mysqldump --add-drop-table --single-transaction -uroot -p -A > alldb.sql   # not blocking innodb
    
**export and archieve**

    mysqldump --add-drop-table --single-transaction -u USER -pPASSWORD DATABASE | gzip > db_name.sql.gz

# Variables

**show variable**  
`SHOW GLOBAL VARIABLES WHERE variable_name = 'max_allowed_packet';`

**set variable**  
`set global max_allowed_packet = 20971520`

# Other

**find mysql config**
```
$ which mysqld
/usr/sbin/mysqld
$ /usr/sbin/mysqld --verbose --help | grep -A 1 "Default options"
```

**optimize database** 

`mysqlcheck -u user -p --optimize database_name`

# Prestashop


**change domain**

    UPDATE ps_shop_url SET domain_ssl="ic-t.alpi.pp.ua" WHERE ps_shop_url.id_shop_url=1;