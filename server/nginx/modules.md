modules
======

### addons
https://github.com/openresty/lua-nginx-module

https://github.com/openresty/stream-lua-nginx-module

https://github.com/openresty/echo-nginx-module

https://github.com/openresty/headers-more-nginx-module

https://github.com/alexazhou/VeryNginx  # WAF, statistic and more

### system

https://github.com/vozlt/nginx-module-sysguard

### network

https://github.com/themagister/Nginx-DOH-Module

https://github.com/yaoweibin/nginx_tcp_proxy_module

### protection

https://github.com/kyprizel/testcookie-nginx-module

### optimization & cache

https://www.newmediacampaigns.com/blog/Automatic-file-minification-on-nginx

https://github.com/nginx-modules/nginx-minify

https://github.com/FRiCKLE/ngx_cache_purge

## core modules
```
--with-file-aio \
--with-threads \                                     
--with-http_addition_module \                        фильтр, добавляющий текст до и после ответа
--with-http_auth_request_module \                    предоставляет возможность авторизации клиента, основанной на результате подзапроса.
--with-http_dav_module \                             предназначен для автоматизации задач управления файлами на сервере по протоколу WebDAV
--with-http_flv_module \                             обеспечивает серверную поддержку псевдо-стриминга для файлов Flash Video (FLV)
--with-http_gunzip_module \                          фильтр, распаковывающий ответы с “Content-Encoding: gzip” для тех клиентов, которые не поддерживают метод сжатия “gzip”
--with-http_gzip_static_module \                     позволяет отдавать вместо обычного файла предварительно сжатый файл с таким же именем и с расширением “.gz”
--with-http_mp4_module \                             обеспечивает серверную поддержку псевдо-стриминга для файлов в формате MP4
--with-http_random_index_module \                    обслуживает запросы, оканчивающиеся слэшом (‘/’), и выдаёт случайный файл в качестве индексного файла каталога
--with-http_realip_module \                          позволяет менять адрес и необязательный порт клиента на переданные в указанном поле заголовка.
--with-http_secure_link_module \                     позволяет проверять аутентичность запрашиваемых ссылок, защищать ресурсы от несанкционированного доступа, ограничивать срок действия ссылок.
--with-http_slice_module \                           разбивает запрос на подзапросы, каждый из которых возвращает определённый диапазон ответа
--with-http_ssl_module \                             обеспечивает работу по протоколу HTTPS.
--with-http_stub_status_module \                     предоставляет доступ к базовой информации о состоянии сервера.
--with-http_sub_module \                             фильтр, изменяющий в ответе одну заданную строку на другую.  sub_filter '<a href="http://127.0.0.1:8080/'  '<a href="https://$host/';
--with-http_v2_module \                              http2
--with-mail \                                        mail server
--with-mail_ssl_module \                             обеспечивает работу почтового прокси-сервера по протоколу SSL/TLS
--with-stream \
--with-stream_realip_module \                        для апстрима
--with-stream_ssl_module \
--with-stream_ssl_preread_module \
--with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic' \
--add-module=/opt/lib/ngx_pagespeed
```



.