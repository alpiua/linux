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
