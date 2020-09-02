generate-sitemap
================

```
#!/bin/bash
SITE=https://vingtsun.in.ua/
FOLDER=/home/www/vingtsun
LINKLIST=/tmp/linklist
SORTEDLIST=/tmp/sortedlist

wget --spider --recursive --level=inf --no-verbose --output-file=$LINKLIST $SITE
grep -i URL $LINKLIST | awk -F 'URL:' '{print $2}' | awk '{$1=$1};1' | awk '{print $1}' | sort -u | sed '/^$/d' > $SORTEDLIST
header='<?xml version="1.0" encoding="UTF-8"?><urlset
      xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9
            http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd">'
echo $header > $FOLDER/sitemap.xml

while read p; do
  case "$p" in
  */ | *.html | *.htm)
    echo '<url><loc>'$p'</loc></url>' >> $FOLDER/sitemap.xml
    ;;
  *)
    ;;
 esac
done < $SORTEDLIST
echo "</urlset>" >> $FOLDER/sitemap.xml
```
