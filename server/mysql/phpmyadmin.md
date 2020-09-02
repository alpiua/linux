phpmyadmin
==========

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
    sudo mv phpMyAdmin-*/ /usr/share/phpmyadmin
    
#### create directories

    sudo mkdir -p /var/lib/phpmyadmin/tmp
    sudo chown -R nginx:nginx /var/lib/phpmyadmin
    sudo mkdir /etc/phpmyadmin/

#### create config & adjust variables

    sudo cp /usr/share/phpmyadmin/config.sample.inc.php  /usr/share/phpmyadmin/config.inc.php
    sudo vim /usr/share/phpmyadmin/config.inc.php

 Set a secret passphrase – Needs to be 32 chars long

 $cfg['blowfish_secret'] = 'H2OxcGXxflSd8JwrwVlh6KW6s2rER63i';

 $cfg['TempDir'] = '/var/lib/phpmyadmin/tmp';

#### nginx config

```
location /myadminalias/ {

      #  auth_basic           "closed site";
      #  auth_basic_user_file ./pass/pma;

     alias /usr/share/phpmyadmin/;

     location ~* ^/myadminalias/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
        alias /usr/share/phpmyadmin/$1;
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
