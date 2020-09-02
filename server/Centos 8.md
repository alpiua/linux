Centos 8
========

### hostname

1. hostnamectl set-hostname <name>

    systemctl restart systemd-hostnamed

    /etc/hosts file.

    `127.0.0.1	tecmint.rhel8`

   This automatically adds an entry by default to the /etc/hostname file.

2. change hostname in /etc/sysctl.conf

    `kernel.hostname=sfpv-othe076.fz.fozzy.lan`

https://developers.redhat.com/blog/2019/05/16/modular-perl-in-red-hat-enterprise-linux-8/

### CERTBOT
dnf config-manager --set-enabled PowerTools
dnf install -y certbot