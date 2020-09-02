Fail2ban
========

# Basic

<https://www.howtoforge.com/tutorial/how-to-install-fail2ban-on-centos/>  
<https://unix.stackexchange.com/questions/171567/installing-fail2ban-on-centos-7>

    $ yum install fail2ban fail2ban-systemd
    $ yum update -y selinux-policy*

copy jail.conf to jail.local and edit it

    $ cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
    $ vim /etc/fail2ban/jail.local 

clean all except settings to change
```
[DEFAULT]
bantime = 7200
banaction = iptables-multiport
```
create filter inside jail.d

```
$ vim /etc/fail2ban/jail.d/sshd.local
[sshd]
enabled = true
#To use more aggressive sshd filter (inclusive sshd-ddos failregex):
#filter = sshd-aggressive
action = firewallcmd-ipset
port    = ssh
logpath = %(sshd_log)s
backend = %(sshd_backend)s
maxretry = 5
bantime  = 86400
```
restart and enable

    service fail2ban restart  
    systemctl enable fail2ban

check jail status

    fail2ban-client status
    
# Upgrades

**Инкрементальный Fail2ban**

    https://habr.com/ru/post/238303/

**Интеграция Fail2ban с CSF для противодействия DDoS на nginx**

    https://habr.com/ru/company/simnetworks/blog/247391/


