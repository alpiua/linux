mysql-users
===========

    grant all privileges on $NAME.* to $NAME@localhost identified by "$PASS";

# Remote user
In order to connect remotely you have to have MySQL bind port 3306 to your machine's IP address in my.cnf. Then you have to have created the user in both localhost and '%' wildcard and grant permissions on database.

**allow remote from certain address**  

    $ vim /etc/my.cnf  
    bind-address= 1.2.3.4  

**create new users for local and remote**

    CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'mypass';
    CREATE USER 'myuser'@'%' IDENTIFIED BY 'mypass';
    GRANT ALL ON *.* TO 'myuser'@'localhost';
    GRANT ALL ON *.* TO 'myuser'@'%';
    flush privileges;

# Change user password
```
mysql > ALTER USER 'user-name'@'localhost' IDENTIFIED BY 'NEW_USER_PASSWORD';  
mysql > FLUSH PRIVILEGES;
```

# Set root password
**if it was not set**

`$ mysqladmin -u root password NEWPASSWORD`

**change current password**

`$ mysql -u root -p'123456' -e 'show databases;'`

**check if password is working**

`$ mysqladmin -u root -p'oldpassword' password newpass`

# Change root password if you forgot it
1. stop mysql service

    `$ service mysql stop && ps awx | grep mysqld`

2. start mysql in safe mode

    `$ mysqld_safe --skip-grant-tables`
    
 3. create new password

    `mysql > update user Password=PASSWORD('NEW_PASSWORD') where User='root';`  
    `mysql > flush privileges;`

# Restoring root (if user corrupted)

**Delete user**
1. Add `'skip-grant-tables'` to `my.cnf` under the [mysqld] section
2. restart mysql
3. type mysql with no password and hit enter
4. mysql > DELETE FROM mysql.user WHERE  user = 'root' AND host = 'localhost'; 

**Create user root**
```
mysql> 
INSERT INTO mysql.user 
SET user = 'root', 
    host = 'localhost', 
    password = Password('NEW_PASSWORD_HERE'), 
    Select_priv = 'y',
    Insert_priv = 'y',
    Update_priv = 'y',
    Delete_priv = 'y',
    Create_priv = 'y',
    Drop_priv = 'y',
    Reload_priv = 'y',
    Shutdown_priv = 'y',
    Process_priv = 'y',
    File_priv = 'y',
    Grant_priv = 'y',
    References_priv = 'y',
    Index_priv = 'y',
    Alter_priv = 'y',
    Show_db_priv = 'y',
    Super_priv = 'y',
    Create_tmp_table_priv = 'y',
    Lock_tables_priv = 'y',
    Execute_priv = 'y',
    Repl_slave_priv = 'y',
    Repl_client_priv = 'y',
    Create_view_priv = 'y',
    Show_view_priv = 'y',
    Create_routine_priv = 'y',
    Alter_routine_priv = 'y',
    Create_user_priv = 'y',
    Event_priv = 'y',
    Trigger_priv = 'y',
    Create_tablespace_priv = 'y';
exit from mysql
remove 'skip-grant-tables' from my.cnf under the [mysqld] section
restart mysql
```
https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html