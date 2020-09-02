compress
========

compress brotli guetzli

# brotli

git clone https://github.com/google/brotli         

```
$ mkdir out && cd out && ../configure-cmake && make
$ make test
$ make install

/usr/local/bin/brotli

for x in `find . -type f \( -name '*.html' -o -name '*.css' -o -name '*.js' \) -size +400c`; do /usr/local/bin/brotli ${x} --force -o ${x}.br; done
```



# zopfli

yum install zopfli

mkdir /root/compress/  
vim /root/compress/static.sh

```
#!/bin/bash

SITEDIR=$1

for x in `find $SITEDIR -type f \( -name '*.html' -o -name '*.css' -o -name '*.js' \) -mmin -10 -size +400c`; do /usr/local/bin/brotli ${x} --force -o ${x}.br; done
for x in `find $SITEDIR -type f \( -name '*.html' -o -name '*.css' -o -name '*.js' \) -mmin -10 -size +400c`; do zopfli -i400 ${x}; done


vim /etc/cron.d/compress 
*/10 * * * * root sh /root/compress/static.sh /home/www/mrcarpets.ru >/dev/null 2>&1 


for x in `find . -type f \( -name '*.gz' -o -name '*.br'\); do rm -rf ${x}`; done
```



# guetzli

cd /$USER && wget https://github.com/google/guetzli/releases/download/v1.0.1/guetzli_linux_x86-64 && chmod +x /$USER/guetzli_linux_x86-64

**первый запуск**
nohup find $SITEURI/img/p/ -type f -mtime +1 -name "*home_default.jpg" -exec /$USER/guetzli_linux_x86-64 --quality 90 '{}' '{}' ';' &

**проверить сколько осталось**  
find /var/www/presta/img/p/ -type f -mtime +1 -name "*home_default.jpg" | wc -l

**добавить в крон для пережатия тех что изменились за день**  
echo "30 1 * * * root /bin/sh find $SITEURI/img/p/ -type f -mtime 1 -name "*home_default.jpg" -exec /$USER/guetzli_linux_x86-64 --quality 90 '{}' '{}' ';' 2>&1; " > /etc/cron.d/img_compress
