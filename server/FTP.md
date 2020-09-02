FTP
=============
vsftp is not equal and should be updated


# vsftpd 

*the case is using virtual users for chrooted ftp directories*

> config placeholder

* [ ] Pam authentification

*login.txt should be created inside the /etc/vsftpd/ directory*

	tee /etc/pam.d/vsftpd.virtual <<-'EOF'  
	auth required pam_userdb.so db=/etc/vsftpd/login  
	account required pam_userdb.so db=/etc/vsftpd/login  
	session required pam_loginuid.so  
	EOF

занести в logins.txt пользователей и пароли
в папке users создать настройки для пользователей фтп (один пользователь
- одна настройка, задаётся папка, в которую у пользователя есть доступ)

useradd.sh

http://pingyaru.blogspot.com/2011/07/vsftpd-linux-mmftp-2.html 
awk -F: '{print}' < /etc/vsftpd_login.txt | db_load -T -t hash /etc/vsftpd_login.db


-- VIRTUAL VSFTPD
awk -F: '{print}' < /etc/vsftpd_login.txt | db_load -T -t hash /etc/vsftpd_login.db


### ETC ###

http://prithak.blogspot.com/2013/07/installation-and-configuration-of.html 

## PRFTPD 
http://habrahabr.ru/sandbox/26850/
http://www.bog.pp.ru/work/vsftpd.html


http://linuxforfun.net/2008/04/15/vsftpd-virtual-users-another-approach/
рабочая инструкция + allow_writeable_chroot=YES
