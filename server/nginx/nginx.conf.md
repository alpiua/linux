nginx.conf
=====
```
load_module modules/ngx_http_brotli_filter_module.so;
load_module modules/ngx_http_brotli_static_module.so;

user nginx;

worker_processes  auto;

#timer_resolution 100ms;
worker_rlimit_nofile 200000;
worker_priority -5;

events {
    worker_connections  20000;
    use epoll;
    multi_accept on;
    accept_mutex on;
}

http {

    # Basic Settings

    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    keepalive_timeout  70;
    keepalive_requests 100;
    send_timeout 2;
    server_tokens off;
    reset_timedout_connection on;
    client_body_timeout 10;
    client_header_timeout 10;
    server_names_hash_bucket_size  64;
    client_max_body_size 100m;

    open_file_cache max=200000 inactive=20s;
    open_file_cache_valid 60s;
    open_file_cache_min_uses 2;
    open_file_cache_errors on;

    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    # GZIP Config

    gzip              on;
    gzip_static       on;
    gzip_min_length   1000;
    gzip_buffers      128 16k;
    gzip_disable      "msie6";
    gzip_proxied      any;
    gzip_comp_level   9;
    gzip_vary         on;
    gzip_http_version 1.1;
    gzip_types
        text/cache-manifest
        text/plain
        text/css
        text/xml
        text/vcard
        text/vnd.rim.location.xloc
        text/vtt
        text/x-component
        text/x-cross-domain-policy
        image/bmp
        image/x-icon
        image/svg+xml
        font/opentype
        application/atom+xml
        application/ld+json
        application/manifest+json
        application/x-javascript
        application/json
        application/xml
        application/rss+xml
        application/vnd.geo+json
        application/vnd.ms-fontobject
        application/javascript
        application/x-font-ttf
        application/font-woff
        application/x-web-app-manifest+json
        application/xhtml+xml;


    brotli_static off;
    brotli on;
    brotli_types text/plain text/css application/json image/svg+xml font/opentype  application/x-javascript text/xml application/xml application/xml+rss application/javascript application/x-font-ttf application/font-woff image/x-icon;
    brotli_buffers 128 16k;
    brotli_min_length 500;
    brotli_comp_level 9;

    # Logs

    log_format main_ext '$remote_addr - $remote_user [$time_local] "$request" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" "$http_x_forwarded_for" ' '"$host" sn="$server_name" ' 'rt=$request_time ' 'ua="$upstream_addr" us="$upstream_status" ' 'ut="$upstream_response_time" ul="$upstream_response_length" ' 'cs=$upstream_cache_status' ;

    access_log off;
    error_log /var/log/nginx/error.log crit;

    # Cache

    proxy_send_timeout 300s;
    proxy_read_timeout 300s;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    include /etc/nginx/conf.d/*.conf;
    include /home/site/config/*.nginx;
}
```