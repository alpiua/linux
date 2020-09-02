curl
====
Manual

	https://curl.haxx.se/docs/httpscripting.html


Login and password from form: 

	curl -c cookie -F "login=name" -F password=pass" https://site.com

OR

	curl -d "username=admin&password=admin&submit=Login" --dump-header headers http://localhost/Login
	curl -L -b headers http://localhost/



Curl POST


https://superuser.com/questions/149329/what-is-the-curl-command-line-syntax-to-do-a-post-request



Погода  

	curl http://v2.wttr.in