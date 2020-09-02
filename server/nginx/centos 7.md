centos 7
=======

Install NGINX

Сборка Nginx с OpenSSL 1.0.2 (ALPN) - для работы http/2 + fastopen
ТОЧНЫЕ ВЕРСИИ ПАКЕТОВ СМОТРЕТЬ НА МОМЕНТ УСТАНОВКИ

** tools

yum -y groupinstall 'Development Tools' &&
yum -y install openssl-devel libxml2-devel libxslt-devel gd-devel perl-ExtUtils-Embed GeoIP-devel pcre-devel zlib-devel

** vars
OPENSSL="openssl-1.0.2t" && NGINX="nginx-1.17.6" 
&& MODSEC="modsecurity-2.9.1"

** openssl unpack 
mkdir -p /opt/lib && wget https://www.openssl.org/source/$OPENSSL.tar.gz -O /opt/lib/$OPENSSL.tar.gz && tar -zxvf /opt/lib/$OPENSSL.tar.gz -C /opt/lib

** pagespeed unpack
bash <(curl -f -L -sS https://ngxpagespeed.com/install) &&
mv /root/incubator-pagespeed-ngx-latest-stable/ /opt/lib/ngx_pagespeed

** brotli dynamic
cd /opt/lib && git clone https://github.com/google/ngx_brotli.git && cd ngx_brotli && git submodule update --init
cd /opt/lib && git clone https://github.com/bagder/libbrotli.git && cd libbrotli
./autogen.sh
./configure
make && make install

** mod security 
yum install gcc make automake autoconf libtool pcre pcre-devel libxml2 libxml2-devel curl curl-devel httpd-devel
cd /opt/lib && wget https://www.modsecurity.org/tarball/2.9.1/$MODSEC.tar.gz && \
gunzip -c $MODSEC.tar.gz | tar xvf - && rm -rf $MODSEC.tar.gz && cd $MODSEC
./configure --enable-standalone-module
make

при ошибке компиляции nginx - patch https://github.com/SpiderLabs/ModSecurity/issues/1418)

** nginx unpack
cd /opt/lib/ && wget http://nginx.org/download/$NGINX.tar.gz ; tar xvzf $NGINX.tar.gz ; rm -rf $NGINX.tar.gz ; cd $NGINX

** копируем строку с настройкой (nginx -V) и добавляем загрузку модулей:
./configure \
--prefix=/etc/nginx \
--sbin-path=/usr/sbin/nginx \
--modules-path=/usr/lib64/nginx/modules \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/run/nginx.lock \
--http-client-body-temp-path=/var/lib/nginx/tmp/clientp \
--http-proxy-temp-path=/var/lib/nginx/tmp/proxy \
--http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi \
--http-uwsgi-temp-path=/var/lib/nginx/tmp/uwsgi \
--http-scgi-temp-path=/var/lib/nginx/tmp/scgi \
--user=nginx \
--group=nginx \
--with-file-aio \
--with-threads \
--with-http_v2_module \
--with-http_ssl_module \
--with-http_sub_module \
--with-http_gzip_static_module \
--with-http_stub_status_module \
--with-http_realip_module \
--with-stream \
--with-stream_realip_module \
--with-stream_ssl_module \
--with-stream_ssl_preread_module \
--with-http_secure_link_module \
--with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -DTCP_FASTOPEN=23' \
--with-ld-opt='-Wl,-z,relro -Wl,-E' \
--with-openssl=/opt/lib/openssl-1.0.2t \
--with-openssl-opt='enable-tlsext zlib no-ssl2 no-shared -fpic' \
--add-module=/opt/lib/ngx_pagespeed \
--add-module=/opt/lib/ngx_brotli \
--add-module=/opt/lib/modsecurity-2.9.1/nginx/modsecurity

если падает - -fPIC в with-cc-opt

make -j4 && make install

yum -y install yum-plugin-versionlock
yum versionlock nginx

### SERVICE ###
echo '
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
# Nginx will fail to start if /run/nginx.pid already exists but has the wrong
# SELinux context. This might happen when running `nginx -t` from the cmdline.
# https://bugzilla.redhat.com/show_bug.cgi?id=1268621
ExecStartPre=/usr/bin/rm -f /run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true

[Install]
WantedBy=multi-user.target ' > /usr/lib/systemd/system/nginx.service

#####################3


useradd --no-create-home nginx



 service nginx restart


Чтобы ХТТП2 сертификат везде-везде работал - нужно ручками добавить домен
https://hstspreload.org
и заголовок на веб-сервер как на сайте скажут


chrome://net-internals/#dns - может поможет скинуть кэш днс хрома ?


** настройка mod_security
cp /opt/lib/modsecurity-2.9.1/modsecurity.conf-recommended /etc/nginx/
cp /opt/lib/modsecurity-2.9.1/unicode.mapping /etc/nginx
cd /etc/nginx/conf.d && mv modsecurity.conf-recommended modsecurity.conf
vim /etc/nginx/modsecurity.conf 
    SecRuleEngine On  
    SecDefaultAction "phase:1,deny,log"   

* установка правил OWASP  (https://modsecurity.org/crs/)
cd /etc/nginx && git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git && \
cd owasp-modsecurity-crs && \
mv crs-setup.conf.example crs-setup.conf && \
mv rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example && rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf && \k
mv rules/RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf.example && rules/RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf

* создать файл в котором добавить в инклюды modsecurity.conf 
tee /etc/nginx/conf.d/modsecurity.inc <<-'EOF'
include /etc/nginx/modsecurity.conf
include /etc/nginx/crs-setup.conf
include /etc/nginx/rules/*.conf
EOF

* добавить в конфиг nginx
location / { ModSecurityEnabled on; ModSecurityConfig modsecurity.inc; }


** удалить софт для сборки
yum -y groupremove 'Development Tools' &&
yum -y remove openssl-devel libxml2-devel libxslt-devel gd-devel perl-ExtUtils-Embed GeoIP-devel pcre-devel zlib-devel




### TCP-FASTOPEN ###
listen 80 fastopen=256;
sysctl -w net.ipv4.tcp_fastopen=3
проверка : 
grep '^TcpExt:' /proc/net/netstat | cut -d ' ' -f 87-92 | column -t


----
сборка rpm
rpm -ivh http://nginx.org/packages/mainline/centos/7/SRPMS/$NGINX.el7.ngx.src.rpm && 
sed -i "s|--with-http_ssl_module|--with-http_ssl_module --with-openssl=/opt/lib/$OPENSSL|g" /root/rpmbuild/SPECS/nginx.spec &&
rpmbuild -ba /root/rpmbuild/SPECS/nginx.spec &&
rpm -Uvh /root/rpmbuild/RPMS/x86_64/$NGINX.el7.centos.ngx.x86_64.rpm 

нечто похожее : https://gist.github.com/moneytoo/ab3f34e4fddc2110675952f8280f49c5



~                                

----- brotli

    # Brotli
    brotli_static off;
    brotli on;
    brotli_types text/plain text/css application/x-javascript text/xml application/xml application/xml+rss application/javascript application/x-font-ttf application/font-woff image/x-icon;
    brotli_buffers 32 4k;
    brotli_min_length 500;
    brotli_comp_level 7;


Deb пакет Nginx - сборка из исходников

https://techlist.top/nginx-deb-package-from-source/

https://blog.programs74.ru/how-to-enable-alpn-on-nginx/

  
 если ругается на openssl:
 в файле nginx-source/auto/options
 USE_OPENSSL=YES OPENSSL=/root/openssl OPENSSL_OPT="no-ssl2 no-ssl3 -fPIC"
 