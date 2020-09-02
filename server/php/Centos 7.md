Centos 7
===========

Install PHP



## REMI REPO
Any PHP VERSION, SEE /etc/yum.repo.d to activate properly
```
rpm -Uvh http://rpms.remirepo.net/enterprise/remi-release-7.rpm && \
yum update && \
yum install yum-utils && \
yum-config-manager --enable remi-php70

yum --enablerepo=remi install phpMyAdmin
```
install

`yum install php php-opcache php-fpm php-gd php-mcrypt php-mysqlnd php-odbc php-pdo php-pecl-apcu php-xml php-mbstring php-intl`


### РАЗНЫЕ ВЕРСИИ ПХП

**several versions the same time**

<https://www.centos.org/forums/viewtopic.php?t=64677>

**5.6**
```
yum install centos-release-scl
php56-php-opcache php56-php-fpm php56-php-gd php56-php-mcrypt php56-php-mysqlnd php56-php-odbc php56-php-pdo php56-php-pecl-apcu php56-php-xml php56-php-mbstring php56-php-intl
```
**find config files:**

`rpm -q --configfiles php54-php-fpm`


### WEBTATIC REPO (not recommended)
install repo

`rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm && yum update`

**7.0**https://webtatic.com/packages/php70/

`yum install php70w php70w-opcache php70w-fpm php70w-gd php70w-mcrypt php70w-mysqlnd php70w-odbc php70w-pdo php70w-pecl-apcu php70w-xml php70w-mbstring php70w-soap`

**7.1**
https://webtatic.com/packages/php71/
```
yum install php71w php71w-opcache php71w-fpm php71w-gd php71w-mcrypt php71w-mysqlnd php71w-odbc php71w-pdo php71w-pecl-apcu php71w-xml php71w-mbstring  
```
**5.6** https://webtatic.com/packages/php70/

`yum install -y php56w php56w-opcache php56w-fpm php56w-gd php56w-mcrypt php56w-mysqlnd php56w-odbc php56w-pdo php56w-pecl-apcu php56w-xml php56w-mbstring`



## запуск сервиса
service php-fpm start && systemctl enable php-fpm

cd /var/lib/php/ && chown -R nginx:nginx ./session && chown -R nginx:nginx ./wsdlcache

проверить в конфиге /etc/php.fpm.d/ путь к сессии

php.ini      cgi.fix_pathinfo=0


#### CHROOT PHP-FPM


vim /etc/php-fpm/www.conf

    chroot = /var/www
    chdir = /

```
fixing php couldn't resolve address:
mkdir /var/www/{etc,lib};
cp /etc/hosts /var/www/etc/hosts;
cp /etc/resolv.conf /var/www/etc/resolv.conf;
```
for 32bit systems:
`cp /lib/libnss_dns.so.2 /var/www/lib/libnss_dns.so.2`
for 64bit systems:
```
cp /lib64/libnss_dns.so.2  /var/www/lib64/libnss_dns.so.2
```

https://forum.antichat.ru/threads/172895/

## xcache
https://www.rosehosting.com/blog/how-to-install-xcache-on-a-centos-7-vps/ 

*yum install -y php56w-devel gcc make  
cd /opt  
wget http://xcache.lighttpd.net/pub/Releases/3.2.0/xcache-3.2.0.tar.gz  
tar -zxvf xcache-3.2.0.tar.gz && cd xcache-3.2.0  
phpize  
./configure --enable-xcache  
make && make install*

$ vim /etc/php.d/xcache.ini

```
[xcache-common]
extension = /usr/lib64/php/modules/xcache.so

[xcache]
	xcache.shm_scheme =    	"mmap"
	xcache.size  =           	32M
	xcache.count =             	1
	xcache.slots =            	8K
	xcache.ttl   =          	3600
	xcache.gc_interval =     	300
	; Same as aboves but for variable cache
	; If you don't know for sure that you need this, you probably don't
	xcache.var_size  =        	0M
	xcache.var_count =         	1
	xcache.var_slots =        	8K
	xcache.var_ttl   =         	0
	xcache.var_maxttl   =      	0
	xcache.var_gc_interval = 	300
	; N/A for /dev/zero
	xcache.readonly_protection = Off
	xcache.mmap_path =	"/dev/zero"
	xcache.cacher =           	On
	xcache.stat   =           	On
```

**adminpage** :

$ cp -a ~/src/xcache/htdocs /var/www/example.com/htdocs/xcache-admin
```
[xcache.admin]
xcache.admin.user = "admin"
xcache.admin.pass = "8e6867a5d05144cf47”   - строка МД5 (тут - xcache)
```

**turn off for current site folder:**
```
xcache.cacher=0
```
The file needs to be named .user.ini (that's a: dot-user-dot-ini)
So to exclude "somefolder" you would put it under /var/www/html/somefolder/.user.ini