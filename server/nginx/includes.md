includes
========

## ssl
/etc/nginx/conf.d/ssl.inc
```
ssl_session_timeout 10m;
ssl_session_cache shared:SSL:50m;
ssl_protocols TLSv1.3 TLSv1.2;
ssl_prefer_server_ciphers on;
ssl_ciphers EECDH:+AES256:-3DES:RSA+AES:RSA+3DES:!NULL:!RC4;
resolver 1.1.1.1 8.8.8.8 valid=300s;
ssl_stapling on;
ssl_stapling_verify on;
resolver_timeout 10s;
```

## headers

/etc/nginx/conf.d/headers.inc
```
add_header Cache-Control public;
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload"; # https://hstspreload.org
add_header X-XSS-Protection "1; mode=block";
add_header X-Frame-Options "SAMEORIGIN";
add_header Referrer-Policy "no-referrer-when-downgrade";
add_header X-Content-Type-Options nosniff;

#add_header Content-Security-Policy "default-src 'self';";
#add_header Access-Control-Allow-Origin *;
#add_header X-Robots-Tag "noindex, nofollow, nosnippet, noarchive"; # Prevent search engines from indexing development server
```
<https://m.habr.com/ru/company/hosting-cafe/blog/315802/>
## static

/etc/nginx/conf.d/static.inc
```
location ~ /\.ht { deny  all; }
location /robots.txt { allow all; log_not_found off; access_log off; }
location ~ \.(htaccess|yml|log|twig|sass|git|tpl)$ { deny all; }

location ~* ^.+\.jpg  { access_log off; error_log off; expires max; gzip off; add_header Cache-Control "public";}
location ~* ^.+\.jpeg { access_log off; error_log off; expires max; gzip off; add_header Cache-Control "public";}
location ~* ^.+\.gif  { access_log off; error_log off; expires max; gzip off; add_header Cache-Control "public";}
location ~* ^.+\.ico  { access_log off; error_log off; expires max; gzip off; add_header Cache-Control "public";}
location ~* ^.+\.png  { access_log off; error_log off; expires max; gzip off; add_header Cache-Control "public";}
location ~* ^.+\.css  { access_log off; expires 31d; add_header Vary Accept-Encoding; add_header Cache-Control "public, must-revalidate, proxy-revalidate";}
location ~* ^.+\.(svg|eot|otf|ttf|woff(?:2)?)  { access_log off; error_log off; expires max; add_header Cache-Control "public";}
``` 
## cloudflare
/etc/nginx/conf.d/cloudflare.inc
```
ssl_client_certificate /etc/nginx/certs/cloudflare.crt;
ssl_verify_client on;

location ~* \.(eot|otf|ttf|woff|woff2)$ {
    add_header Access-Control-Allow-Origin *;
}

set_real_ip_from 173.245.48.0/20;
set_real_ip_from 103.21.244.0/22;
set_real_ip_from 103.22.200.0/22;
set_real_ip_from 103.31.4.0/22;
set_real_ip_from 141.101.64.0/18;
set_real_ip_from 108.162.192.0/18;
set_real_ip_from 190.93.240.0/20;
set_real_ip_from 188.114.96.0/20;
set_real_ip_from 197.234.240.0/22;
set_real_ip_from 198.41.128.0/17;
set_real_ip_from 162.158.0.0/15;
set_real_ip_from 104.16.0.0/12;
set_real_ip_from 172.64.0.0/13;
set_real_ip_from 131.0.72.0/22;
set_real_ip_from 2400:cb00::/32;
set_real_ip_from 2606:4700::/32;
set_real_ip_from 2803:f800::/32;
set_real_ip_from 2405:b500::/32;
set_real_ip_from 2405:8100::/32;
set_real_ip_from 2a06:98c0::/29;
set_real_ip_from 2c0f:f248::/32;
real_ip_header CF-Connecting-IP;
```

## prestashop 1.6
```
location ~ \.tpl$ { deny all; return 404; }

rewrite ^/[a-zA-Z][a-zA-Z]/index.php(.*)$ /index.php$1;
rewrite ^/api/?(.*)$ /webservice/dispatcher.php?url=$1 last;
rewrite ^/([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.jpg$ /img/p/$1/$1$2$3.jpg last;
rewrite ^/([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.jpg$ /img/p/$1/$2/$1$2$3$4.jpg last;
rewrite ^/([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.jpg$ /img/p/$1/$2/$3/$1$2$3$4$5.jpg last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.jpg$ /img/p/$1/$2/$3/$4/$1$2$3$4$5$6.jpg last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.jpg$ /img/p/$1/$2/$3/$4/$5/$1$2$3$4$5$6$7.jpg last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$1$2$3$4$5$6$7$8.jpg last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$7/$1$2$3$4$5$6$7$8$9.jpg last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$7/$8/$1$2$3$4$5$6$7$8$9$10.jpg last;
rewrite ^/c/([0-9]+)(\-[\.*_a-zA-Z0-9-]*)(-[0-9]+)?/.+\.jpg$ /img/c/$1$2$3.jpg last;
rewrite ^/c/([a-zA-Z_-]+)(-[0-9]+)?/.+\.jpg$ /img/c/$1$2.jpg last;
rewrite ^/images_eei?([^/]+)\.(jpe?g|png|gif)$ /js/jquery/plugins/fancybox/images/$1.$2 last;
rewrite ^/page-not-found$ /index.php?controller=404 last;

rewrite ^/([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.webp$ /img/p/$1/$1$2$3.webp last;
rewrite ^/([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.webp$ /img/p/$1/$2/$1$2$3$4.webp last;
rewrite ^/([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.webp$ /img/p/$1/$2/$3/$1$2$3$4$5.webp last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.webp$ /img/p/$1/$2/$3/$4/$1$2$3$4$5$6.webp last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.webp$ /img/p/$1/$2/$3/$4/$5/$1$2$3$4$5$6$7.webp last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.webp$ /img/p/$1/$2/$3/$4/$5/$6/$1$2$3$4$5$6$7$8.webp last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.webp$ /img/p/$1/$2/$3/$4/$5/$6/$7/$1$2$3$4$5$6$7$8$9.webp last;
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(\-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+\.webp$ /img/p/$1/$2/$3/$4/$5/$6/$7/$8/$1$2$3$4$5$6$7$8$9$10.webp last;
rewrite ^/c/([0-9]+)(\-[\.*_a-zA-Z0-9-]*)(-[0-9]+)?/.+\.webp$ /img/c/$1$2$3.webp last;
rewrite ^/c/([a-zA-Z_-]+)(-[0-9]+)?/.+\.webp$ /img/c/$1$2.webp last;

location /as4_seositemap {
    rewrite ^/as4_seositemap-([0-9]+).xml$ /modules/pm_advancedsearch4/sitemap/seositemap-$1.xml break;
}
```

## prestashop 1.7

```
location /adminshop/ {
    if (!-e $request_filename) {
        rewrite ^/.*$ /adminshop/index.php last;
	}
}

location /upload {
    location ~ \.php$ {
        deny all;
    }
}

location /img {
    location ~ \.php$ {
        deny all;
    }
}

location ~ ^/(app|bin|cache|classes|config|controllers|docs|localization|override|src|tests|tools|translations|travis-scripts|vendor|var)/ {
    deny all;
}

location ~ ^/modules/.*/vendor/ {
    deny all;
}

error_page 404 /index.php?controller=404; 
rewrite ^/([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$1$2$3.jpg last; 
rewrite ^/([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$1$2$3$4.jpg last; 
rewrite ^/([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$1$2$3$4$5.jpg last; 
rewrite ^/([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$1$2$3$4$5$6.jpg last; 
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$5/$1$2$3$4$5$6$7.jpg last; 
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$1$2$3$4$5$6$7$8.jpg last; 
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$7/$1$2$3$4$5$6$7$8$9.jpg last; 
rewrite ^/([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])([0-9])(-[_a-zA-Z0-9-]*)?(-[0-9]+)?/.+.jpg$ /img/p/$1/$2/$3/$4/$5/$6/$7/$8/$1$2$3$4$5$6$7$8$9$10.jpg last; 
rewrite ^/c/([0-9]+)(-[.*_a-zA-Z0-9-]*)(-[0-9]+)?/.+.jpg$ /img/c/$1$2$3.jpg last; 
rewrite ^/c/([a-zA-Z_-]+)(-[0-9]+)?/.+.jpg$ /img/c/$1$2.jpg last; 
rewrite ^/images_ie/?([^/]+).(jpe?g|png|gif)$ /js/jquery/plugins/fancybox/images/$1.$2 last; # testing 
rewrite ^/order$ /index.php?controller=order last;      # testing 

rewrite ^/api/?(.*)$ /webservice/dispatcher.php?url=$1 last; 
rewrite ^(/install(?:-dev)?/sandbox)/(.*) /$1/test.php last; 
```