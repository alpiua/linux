disk-speed
==========

testdrive.sh
```
#!/bin/sh
sync; dd if=/dev/zero of=tempfile bs=1M count=8192 conv=fdatasync
dd if=tempfile of=/dev/null bs=1M count=8192
sudo /sbin/sysctl -w vm.drop_caches=3
dd if=tempfile of=/dev/null bs=1M count=8192
rm tempfile
```
