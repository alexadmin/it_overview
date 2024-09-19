```
echo "- - -" > /sys/class/scsi_host/host0/scan # host1, host2 ....
fdisk -l
pvcreate /dev/sdx
vgextend vg00 /dev/sdx
lvextend -l +100%FREE -r /dev/vg00/var
```
