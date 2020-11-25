phpmyadmin
==========
### nginx config

```
set $pma /pma3388/

location /$pma/ {

      #  auth_basic           "closed site";
      #  auth_basic_user_file ./pass/pma;

     alias /usr/share/phpMyAdmin/;

     location ~* ^/$pma/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
        alias /usr/share/phpMyAdmin/$1;
        }
     
     location ~ /(libraries|setup) {
        return 404;
        }
        
     location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(.*)$;
        fastcgi_pass   unix:/var/run/php-fpm/pma.sock;
        fastcgi_index  index.php;
        
        include fastcgi_params;
        fastcgi_param  HTTPS on;
        fastcgi_param SCRIPT_FILENAME $request_filename;
        fastcgi_param  HTTP_MOD_REWRITE On;
        
        fastcgi_connect_timeout 300s;
        fastcgi_send_timeout 300s;
        fastcgi_read_timeout 12000s;
        fastcgi_ignore_client_abort off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
        }
}
```

### config php-fpm
```
[phpmyadmin]

listen = /var/run/php-fpm/pma.sock

listen.owner = nginx
listen.group = nginx
listen.mode = 0666

user = pma
group = pma

pm = dynamic

pm.max_children = 1
pm.start_servers = 1
pm.process_idle_timeout = 30s
pm.min_spare_servers = 1
pm.max_spare_servers = 1
pm.max_requests = 0

env[PATH] = /usr/local/bin:/usr/bin:/bin

php_flag[display_errors] = on
php_admin_value[error_log] = /var/log/php-fpm/phpmyadmin-error.log
php_admin_flag[log_errors] = on
php_admin_value[memory_limit] = 128M

; Set session path to a directory owned by process user
php_value[session.save_handler] = files
php_value[session.save_path]    = /var/lib/phpMyAdmin/session
php_value[soap.wsdl_cache_dir]  = /var/lib/phpMyAdmin/wsdlcache
```

### INSTALL

(c) <https://computingforgeeks.com/install-and-configure-phpmyadmin-on-rhel-8/>

#### save version
```
```
DATA="$(wget https://www.phpmyadmin.net/home_page/version.txt -q -O-)"
URL="$(echo $DATA | cut -d ' ' -f 3)"
VER="$(echo $DATA | cut -d ' ' -f 1)"
```
```
#### download
    curl -o phpMyAdmin-${VER}-all-languages.tar.gz https://files.phpmyadmin.net/phpMyAdmin/${VER}/phpMyAdmin-${VER}-all-languages.tar.gz

#### extract

    tar xvf phpMyAdmin-${VER}-english.tar.gz
OR
    tar xvf phpMyAdmin-${VER}-all-languages.tar.gz

#### move to /usr/share/phpmyadmin

    rm phpMyAdmin-*.tar.g
    sudo mv phpMyAdmin-*/  /usr/share/phpMyAdmin

#### create system user
useradd -r pma -s /sbin/nologin
    
#### create config & adjust variables

    sudo cp /usr/share/phpMyAdmin/config.sample.inc.php  /usr/share/phpMyAdmin/config.inc.php
    sudo chown -R pma:pma /usr/share/phpMyAdmin/
    sudo vim /usr/share/phpMyAdmin/config.inc.php

 Set a secret passphrase – Needs to be 32 chars long
 $cfg['blowfish_secret'] = 'H2OxcGXxflSd8JwrwVlh6KW6s2rER63i';
 $cfg['TempDir'] = '/var/lib/phpMyAdmin/tmp';
    
    
#### create directories

    sudo mkdir -p /var/lib/phpMyadmin/{tmp,upload,download,session,wsdlcache}
    sudo chown -R pma:pma /var/lib/phpMyAdmin
    sudo mkdir /etc/phpMyAdmin/

