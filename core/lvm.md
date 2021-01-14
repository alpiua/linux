lvm
===

###  physical volume
`pvcreate /dev/sda /dev/sdb`

`pvs` - list 

`pvdisplay [/dev/sdX]` 

### volume group
`vgcreate vg00 /dev/sdb /dev/sdc`

`vgdisplay vg00`

`vgextend vg00 /dev/sdd` - extend volume group

### logical volume
`lvcreate -n vol_projects -L 10G vg00`

`lvcreate -n vol_backups -l 100%FREE vg00`

`lvs`

`lvdisplay`

`lvdisplay vg00/vol_projects`

`lvreduce -L -2.5G -r /dev/vg00/vol_projects` - reduce

`lvextend -l +100%FREE -r /dev/vg00/vol_backups` - extend

### file sysyem and mount 
`mkfs.ext4 /dev/vg00/vol_projects`

`mkdir /home/projects`

update /etc/fstab

`mount -a`
