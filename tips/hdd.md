## Work with HDD
### LVM
#### create LV partition at free space
```
lvcreate -n LV_NAME -l 100 %FREE VG_NAME
mkfs.ext4 /dev/mapper/LV_NAME
```
#### Уменьшение lv, создание нового
```
umount /opt
lvreduce -L 1G -r /dev/mapper/39--VS--ISMGDAPP01--vg-opt
lvcreate -n app -l 100%FREE 39-VS-ISMGDAPP01-vg
mkfs -t ext4 /dev/39-VS-ISMGDAPP01-vg/app
vim /etc/fstab
mount -a
```
#### rename VolumeGroup LVM
1. Reboot your machine and choose recovery mode (not mandatory but it’s better)
2. Run `vgrename`
```
vgrename actualName NewName
```
3. Edit `/etc/fstab` and change all the entries with the new name
4. Edit `/etc/initramfs-tools/conf.d/resume` and replace the old name with the new one
5. Edit `/boot/grub/grub.cfg` and replace the old name with the new one
6. Rebuild an initramfs:
```
update-initramfs -u -k all
```
7. Reboot
8. In memory:
 * [https://www.thegeekdiary.com/centos-rhel-7-how-to-rename-the-volume-group-for-root-and-swap/](https://www.thegeekdiary.com/centos-rhel-7-how-to-rename-the-volume-group-for-root-and-swap/)
#### 
```
lsblk -o NAME,FSTYPE,LABEL,MOUNTPOINT,SIZE
```
#### add new hdd at LVM without rebooting
```
ls /sys/class/scsi_host
echo "- - -" > /sys/class/scsi_host/host0/scan
fdisk -l
tail -f /var/log/message
fdsik /dev/sdX
n -> p -> 1
t -> 8e -> w
vgextend VG_NAME /dev/sdX1
```
