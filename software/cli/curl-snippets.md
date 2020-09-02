curl-snippets
========================
### download file from google drive
	curl https:/site.esclick.me/BqXq3pfZJJuu -L \
	| egrep "https:\/\/drive\.google\.com\/file\/d\/(\w|-){26,}\/view"


### find file in in raw mail and download from dropbox
	LINKS=$(tr --delete '=\n' < $MAILFILE | egrep -o "https:\/\/site\.esclick\.me\/\w{12}")
	
	for LINK in $LINKS                                                                                               
	do                                                                                                               
    URL=$(curl -Ls -o /dev/null -w %{url_effective} $LINK)                                                       
    [[ $URL == *dropboxusercontent*file ]] && $CURL $URL > "$FOLDER/filename.xls"                             
	done


### download specific file with cyrillic name behind the form

	FILE=$(curl -c cookie -X POST -F "login=login" -F "pass=password" https:/site.com -L \ 
	| iconv -f cp1251 -t utf8 - \
	| egrep -o 'Прайс.*\.htm' \
	| egrep -o "\/download\/f\/[0-9]{4,}\.htm")
	
	curl -b cookie https://site.com$FILE -o filename.xls
	
	
### test site response speed
`curl -w "Connect: %{time_connect} TTFB: %{time_starttransfer} Total time: %{time_total}" https://site.com`

simple script
```
#/bin/bash
SITE="$1"
for i in {1..5}; do curl -w "Connect: %{time_connect} TTFB: %{time_starttransfer} Total time: %{time_total}\n" -I https://108d.ru/contact-us >> ttfb; done
clear
echo "Result for site $SITE:"
cat ttfb | grep Connect:
rm ttfb
```

### check if url exists
`if curl --output /dev/null --silent --head --fail "$url"; then echo "URL exists: $url" else echo "URL does not exist: $url" fi`

If server refuses HEAD requests, an alternative is to request only the first byte of the file:

`if curl --output /dev/null --silent --fail -r 0-0 "$url"; then`

curl -u "office@traverse.com.ua:traverse" "https://avantmarket.com.ua/wa-data/public/site/Excel/`date +'05.%m.%Y'`%20Avantmarket%20price.xls" -o ./1.xls

### send email 

```
curl --url 'smtps://smtp.gmail.com:465' --ssl-reqd \
  --mail-from 'username@gmail.com' --mail-rcpt 'john@example.com' \
  --upload-file mail.txt --user 'username@gmail.com:password' --insecure
```

  
[email with image attached](https://stackoverflow.com/questions44728855/curl-send-html-email-with-embedded-image-and-attachment) 

### SFTP
**Upload**

`curl  -k "sftp://83.46.38.23:22/CurlPutTest/" --user "testuser:testpassword" -T "C:\test\testfile.xml" --ftp-create-dirs`

**Download**

`curl  -k "sftp://83.46.38.23:22/CurlPutTest/testfile.xml" --user "testuser:testpassword" -o "C:\test\testfile.xml" --ftp-create-dirs`

**Rename**

`curl  -k "sftp://83.46.38.23:22/CurlPutTest/" --user "testuser:testpassword" -Q "-RENAME ‘/CurlPutTest/testfile.xml’ ‘/CurlPutTest/testfile.xml.tmp’" --ftp-create-dirs`

**Delete**

`curl  -k "sftp://83.46.38.23:22/CurlPutTest/ " --user "testuser:testpassword" -Q "–RM /CurlPutTest/testfile.xml" --ftp-create-dirs`

**MKdir**

`curl  -k "sftp://83.46.38.23:22/CurlPutTest/test " --user "testuser:testpassword" -Q "–MKDIR /CurlPutTest/Test" --ftp-create-dirs`

**RMdir**

`curl  -k "sftp://83.46.38.23:22/CurlPutTest/test " --user "testuser:testpassword" -Q "–RMDIR /CurlPutTest/Test" --ftp-create-dirs`