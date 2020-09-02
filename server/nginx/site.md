site
======
```php
server {
    listen 80 fastopen=256;         # sysctl -w net.ipv4.tcp_fastopen=3
    server_name site.com;
    return 301 https://site.com$request_uri;
}

server {
    listen 443 ssl http2 fastopen=256;
    server_name site.com;

    #SSL
    ssl_certificate /etc/letsencrypt/live/site.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/site.com/privkey.pem;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    include /etc/nginx/conf.d/ssl.inc;
    include /etc/nginx/conf.d/deny.inc;
    include /etc/nginx/conf.d/headers.inc;
    include /etc/nginx/conf.d/cloudflare.inc;
    include /etc/nginx/conf.d/redirect.inc;
    include /etc/nginx/conf.d/static.inc;
    include /etc/nginx/conf.d/prestashop16.inc;
    include /etc/nginx/conf.d/phpmyadmin.inc;

    charset utf-8;
    root /home/site/www;

    location / {
        index index.php;
        try_files $uri $uri/ /index.php$is_args$args;
    }

    client_max_body_size            100m;
    client_body_buffer_size         1m;
    client_header_buffer_size       2k;
    large_client_header_buffers     4 8k;

    location ~ \.php$ {
        try_files                   $fastcgi_script_name =404;
        fastcgi_pass                unix:/var/run/php-fpm.sock;
        fastcgi_index               index.php;
        fastcgi_keep_conn           on;
        fastcgi_intercept_errors    on;
        fastcgi_ignore_headers      "Cache-Control" "Expires";
        fastcgi_param               SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include                     fastcgi_params;

        fastcgi_connect_timeout     300s;
        fastcgi_send_timeout        300s;
        fastcgi_read_timeout        1200s;
        fastcgi_buffer_size         1m;
        fastcgi_buffers             8 8m;
        fastcgi_cache_valid 200 301 302 1h;
        fastcgi_cache_valid 404 500 501 502 503 504 505 506 507 509 510 1m;
        fastcgi_cache_valid any     1m;
        
        # Temp file tweak
        fastcgi_max_temp_file_size   0;
        fastcgi_temp_file_write_size 256k;
    }

    access_log /home/site/logs/site.access.log;
    error_log  /home/site/logs/site.error.log;
}
```