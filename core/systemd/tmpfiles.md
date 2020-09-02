tmpfiles
========

### чистить темп при загрузке

vim /etc/tmpfiles.d/tmp.conf

    R! /tmp/* 1777 root root 1d
    R! /var/tmp/* 1777 root root 1d

### lastlog fix
/etc/tmpfiles.d/var-part.conf

`f /var/log/lastlog 0664 root utmp -`