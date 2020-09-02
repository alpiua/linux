centos 8
================

dnf install dnf-utils

vim /etc/yum.repos.d/nginx.repo 

```
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true 

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```

sudo yum-config-manager --enable nginx-mainline

dnf install nginx

### install brotli

dnf install -y pcre pcre-devel zlib zlib-devel openssl openssl-devel
dnf install -y tar zip git socat && sudo dnf groupinstall "Development Tools"

cd /opt/ && wget https://nginx.org/download/nginx-1.19.1.tar.gz && tar zxvf nginx-1.19.1.tar.gz

git clone https://github.com/google/ngx_brotli.git
cd ngx_brotli && git submodule update --init && cd ../nginx


./configure --with-compat --add-dynamic-module=../ngx_brotli
make modules
cp objs/*.so /etc/nginx/modules
chmod 644 /etc/nginx/modules/*.so

vim /etc/nginx/nginx.conf
```
load_module modules/ngx_http_brotli_filter_module.so;
load_module modules/ngx_http_brotli_static_module.so;
```


dnf remove -y pcre-devel zlib-devel openssl-devel && dnf groupremove 'Development Tools'