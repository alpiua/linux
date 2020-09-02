dnf / yum
=========

yum --disablerepo="*" --enablerepo="remi-safe" list available | more
yum --disablerepo="*" --enablerepo="remi-safe" search php


dnf module reset php