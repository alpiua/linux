secure
======

**https://www.snort.org/**


Hardening server security

Базовая авторизация пхп:
https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/

Базы уязвимостей:
https://cve.mitre.org/
https://www.exploit-db.com/

https://www.owasp.org/images/1/19/OTGv4.pdf


1) rkhunter
yum --enablerepo=epel -y install rkhunter
vi /etc/sysconfig/rkhunter

rkhunter --update && rkhunter --propupd

#execute checking
#--sk means sikpping to push Enter key
#if specified --rwo , display only warnings
rkhunter --check --sk



2) AIDE

yum -y install aide

vi /etc/aide.conf
      # for example, change setting of monitoring /var/log
      /var/log   p+u+g+i+n+acl+selinux+xattrs

aide --init

#copy generated DB to master DB
cp -p /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz

aide --check
#after perfoming check and solving issues must be update
aide --update

vi /etc/cron.d/aide
        00 01 * * * /usr/sbin/aide --update | mail -s 'Daily Check by AIDE' root

CSF 

3) Clamscan

yum -y install clamav clamav-db (yum install clamav-server clamav-data clamav-update clamav-filesystem clamav clamav-scanner-systemd clamav-devel clamav-lib clamav-server-systemd)

cp /usr/share/clamav/template/clamd.conf /etc/clamd.d/clamd.conf

        Удалить строку Example
        User clamscan  #пользователь кому разрешен запуск
        LocalSocket /var/run/clamd.<SERVICE>/clamd.sock

vim /etc/sysconfig/freshclam 
        FRESHCLAM_DELAY=180

vim /etc/freshclam.conf
        Удалить строку Example

freshclam (обновляем базы)

vim /usr/lib/systemd/system/clam-freshclam.service (создаём новый)

        #Script for run the freshclam as daemon
        [Unit]
        Description = freshclam scanner
        After = network.target
        [Service]
        Type = forking
        ExecStart = /usr/bin/freshclam -d -c 4
        Restart = on-failureш
        PrivateTmp = true
        [Install]
        WantedBy=multi-user.target

systemctl enable clam-freshclam.service
systemctl start clam-freshclam.service
systemctl status clam-freshclam.service



4) Maldet

wget -c https://www.rfxn.com/downloads/maldetect-current.tar.gz
tar -xzf maldetect-current.tar.gz
cd maldetect-*
sh ./install.sh
maldet --update-ver && maldet --update

vim /usr/local/maldetect/conf.maldet
        email_alert=1
        email_addr="ящик

maldet -a /home/www/

tail -f /usr/local/maldetect/event_log


Переместить вирусы на карантин:
#maldet -q 111914-0905.31288
Восстановить файлы с карантина:
#maldet -s 111914-0905.31288

CRON задания прописаны в файле
/etc/cron.daily/maldet
можно изменить пути для сканирования своих директорий.

Добавить исключающие пути для обхода сканирования можно в
/usr/local/maldetect/ignore_paths


