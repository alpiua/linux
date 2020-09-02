journald
========

https://selectel.ru/blog/upravlenie-loggirovaniem-v-systemd/

journalctl -b -p err|less

journalctl --list-boot

journalctl --disk-usage


/etc/systemd/journald.conf
```
[Journal]
Storage=persistent
Compress=yes
#Seal=yes
#SplitMode=uid
#SyncIntervalSec=5m
#RateLimitInterval=30s
#RateLimitBurst=1000
SystemMaxUse=500M
#SystemKeepFree=
#SystemMaxFileSize=50M
#SystemMaxFiles=100
#RuntimeMaxUse=
#RuntimeKeepFree=
#RuntimeMaxFileSize=
#RuntimeMaxFiles=100
MaxRetentionSec=2week
MaxFileSec=1day
#ForwardToSyslog=yes
#ForwardToKMsg=no
ForwardToConsole=no
ForwardToWall=no
#TTYPath=/dev/tty12
#MaxLevelStore=debug
#MaxLevelStore=notice
#MaxLevelSyslog=debug
#MaxLevelKMsg=notice
#MaxLevelConsole=info
```