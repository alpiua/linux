lvm
===

```sh
pvcreate /dev/sdb
vgcreate dbvg /dev/sdb
lvcreate -v -W y -Z y -l 100%VG -n lv_db dbvg
mkfs.ext4 /dev/mapper/dbvg-lv_db <---Вот тут вопрос. Может под БД xfs лучше?
echo "/dev/mapper/dbvg-lv_db /var/lib/mysql  ext4  defaults  1 2" >> /etc/fstab
mount /var/lib/mysql
```

выбор фс
https://www.percona.com/blog/2018/07/03/linux-os-tuning-for-mysql-database-performance/
https://fuzzyblog.io/blog/mysql/2014/08/28/optimizing-your-filesystem-for-mysql.html

perfomance, journaling
https://serverfault.com/questions/363355/io-wait-causing-so-much-slowdown-ext4-jdb2-at-99-io-during-mysql-commit



### расширение

```sh
Надо расширить /dev/mapper/datavg-lv_ocdata.
Смотрим, что места нет в VG datavg
# pvs
PV         VG     Fmt  Attr PSize   PFree
  /dev/sda2  rootvg lvm2 a--   29,70g   8,77g
  /dev/sdb1  datavg lvm2 a--  100,00g      0
  /dev/sdc1  datavg lvm2 a--  100,00g      0
  /dev/sdd   datavg lvm2 a--  100,00g      0
  /dev/sde   datavg lvm2 a--  100,00g      0
  /dev/sdf   datavg lvm2 a--  100,00g      0
  /dev/sdg   datavg lvm2 a--  100,00g      0
  /dev/sdh   datavg lvm2 a--  100,00g  50,00g <--- это все...

Поэтому пишем письмо на servicedesk@fozy.ua и в копию srvsys@fozzy.ua. Можно и нас.
Итак. Нам как и просили, дали еще один диск на 100Гиг, который надо всунуть в LVM
Поехали :)

Смотрим что с дисками и не видем нового...
 # ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdb  /dev/sdb1  /dev/sdc  /dev/sdc1  /dev/sdd  /dev/sde  /dev/sdf  /dev/sdg  /dev/sdh

Делаем рескан...
# cd /sys/class/scsi_host/ && for i in host?; do echo "- - -" > /sys/class/scsi_host/$i/scan; done

Проверяем и видем, что появился /dev/sdi
# ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdb  /dev/sdb1  /dev/sdc  /dev/sdc1  /dev/sdd  /dev/sde  /dev/sdf  /dev/sdg  /dev/sdh  /dev/sdi

Создаем PV на диске
# pvcreate /dev/sdi

Расширяем VG datavg
# vgextend datavg /dev/sdi

Проверяем
 # pvs
  PV         VG     Fmt  Attr PSize   PFree
  /dev/sda2  rootvg lvm2 a--   29,70g   8,77g
  /dev/sdb1  datavg lvm2 a--  100,00g      0
  /dev/sdc1  datavg lvm2 a--  100,00g      0
  /dev/sdd   datavg lvm2 a--  100,00g      0
  /dev/sde   datavg lvm2 a--  100,00g      0
  /dev/sdf   datavg lvm2 a--  100,00g      0
  /dev/sdg   datavg lvm2 a--  100,00g      0
  /dev/sdh   datavg lvm2 a--  100,00g  50,00g
  /dev/sdi   datavg lvm2 a--  100,00g 100,00g

Расширяем:
Смотрим что есть...
# df -h
Файловая система                                                                  Размер Использовано  Дост Использовано% Cмонтировано в
/dev/mapper/datavg-lv_ocdata                                                        620G         595G   23G           97% /opt/oc_data

Расширяем ФС
lvresize -d -v -r -L +1G /dev/mapper/datavg-lv_ocdata
или
lvresize -v -r -L+1G datavg/lv_ocdata

```