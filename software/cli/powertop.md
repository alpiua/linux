powertop
========

# sh script to change settings

```
echo 'min_power' > '/sys/class/scsi_host/host4/link_power_management_policy';
echo 'min_power' > '/sys/class/scsi_host/host5/link_power_management_policy';
echo '0' > '/proc/sys/kernel/nmi_watchdog';
echo 'auto' > '/sys/bus/i2c/devices/i2c-2/device/power/control';
echo 'auto' > '/sys/bus/usb/devices/2-4/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:1f.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:00.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:02:00.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:1c.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:1d.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:1a.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:1c.1/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:19.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:03:00.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:14.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:1b.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:16.0/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:1f.2/power/control';
echo 'auto' > '/sys/bus/pci/devices/0000:00:16.3/power/control';
echo '1500' > '/proc/sys/vm/dirty_writeback_centisecs';
echo 'min_power' > '/sys/class/scsi_host/host3/link_power_management_policy';
echo 'min_power' > '/sys/class/scsi_host/host1/link_power_management_policy';
echo 'min_power' > '/sys/class/scsi_host/host2/link_power_management_policy';
echo 'min_power' > '/sys/class/scsi_host/host0/link_power_management_policy';
ethtool -s eth0 wol d;
iw dev wlan0 set power_save on
```