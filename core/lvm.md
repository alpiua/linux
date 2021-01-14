lvm
===

###  physical volume
`pvcreate /dev/sda /dev/sdb`
`pvs` - list 
`pvdisplay [/dev/sdX]` 

### volume group
`vgcreate vg00 /dev/sdb /dev/sdc`
`vgdisplay vg00`

### logical volume
`lvcreate -n vol_projects -L 10G vg00`
`lvcreate -n vol_backups -l 100%FREE vg00`
`lvs`
`lvdisplay`
`lvdisplay vg00/vol_projects`