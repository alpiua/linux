certbot
=======

## certbot nginx plugin 
source: https://certbot.eff.org/lets-encrypt/centosrhel7-nginx.html

**install centos7**
```
$ yum -y install yum-utils
$ yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
$ yum install certbot python2-certbot-nginx
```
**install centos8**
```
$ dnf install python3-pip
$ pip3 install certbot-nginx
```
**usage**
```
$ certbot --nginx certonly
$ certbot --nginx certonly
```
## old method

    $SITE=site.com

**add to site nginx config (80 port)**

    location /.well-known/acme-challenge { alias /home/www/.well-known/acme-challenge; }
**apply config**

    $ nginx -s reload

**run certbot**

    $ certbot certonly -a webroot --webroot-path /home/www/ -d $SITE -d www.$SITE --server https://acme-v01.api.letsencrypt.org/directory


## APACHE
```
sudo apt-get install apache2 
python-letsencrypt-apache
certbot --apache
```
https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-centos-7
## Misc
**create dhparam**  

    $ openssl dhparam -out /etc/letsencrypt/live/$SITE/dhparam.pem 2048

**update cert**

    $ certbot renew
    
--force  
--dry-run