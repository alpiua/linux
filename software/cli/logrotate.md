logrotate
=========

vim /etc/logrotate.d/weblogs.conf

/home/sitename/logs/site* {
    rotate 30
    size=10M
    missingok
    notifempty
    daily
    compress
    delaycompress
    maxage 30
    olddir /home/sitename/logs/old
    postrotate
        service rsyslog restart > /dev/null
    endscript
}

logrotate -f /etc/logrotate.conf