_users
=====
### MYSQL 8

CREATE USER 'yourUserName'@'localhost' IDENTIFIED BY 'yourPassword';
GRANT ALL ON *.* TO 'yourUserName'@'localhost';
flush privileges;

Reset ROOT
+  https://stackoverflow.com/questions/50691977/how-to-reset-the-root-password-in-mysql-8-0-11
+  https://www.tecmint.com/reset-root-password-in-mysql-8/h

### create user sh
```
 tee create_db.sh <<-'EOF'
 NAME=[ИМЯ БАЗЫ, ПОЛЬЗОВАТЕЛЯ]
 PASS=[ПАРОЛЬ БАЗЫ]
 mysql -u root -p -e 'CREATE DATABASE ${NAME};'
 mysql -u root -p -e 'grant all privileges on ${NAME}.* to ${NAME}@localhost identified by "${PASS}";'
 mysql -u root -p -e 'flush privileges;'
 EOF
 ```