centos 7
========


Install mariadb


# CENTOS 7

## Добавление репозитария

```
echo '
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.3/rhel7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1' > /etc/yum.repos.d/Mariadb.repo
```

## установка
```
yum -y install mariadb-server mariadb && \
systemctl start mysql && \
mysql_secure_installation
```

## mysqltuner

```
yum -y install mysqltuner

```


https://forums.cpanel.net/threads/mysql-my-cnf-tuning-me-too.239611/

# создание БД
tee /home/alpi/create_db.sh <<-'EOF'
NAME=[ИМЯ БАЗЫ, ПОЛЬЗОВАТЕЛЯ]
PASS=[ПАРОЛЬ БАЗЫ]
mysql -u root -p -e 'CREATE DATABASE $NAME;'
mysql -u root -p -e 'grant all privileges on $NAME.* to $NAME@localhost identified by "$PASS";'
mysql -u root -p -e 'flush privileges;'
EOF

Create database dbname; 
GRANT ALL PRIVILEGES ON dbname.* TO 'username'@'localhost' IDENTIFIED BY 'password';
Flush privileges;

http://php.net/manual/ru/    закончить настройку
поставить пароль на доступ к пхпмайадмин