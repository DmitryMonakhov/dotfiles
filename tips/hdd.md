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
1. Run `vgrename`
```
vgrename actualName NewName
```
2. Edit `/etc/fstab` and change all the entries with the new name
If root LVM changed then:
3. Edit `/etc/initramfs-tools/conf.d/resume` and replace the old name with the new one
4. Edit `/boot/grub/grub.cfg` and replace the old name with the new one
5. Rebuild an initramfs:
```
update-initramfs -u -k all
```
7. Reboot
8. In memory:
 * [https://www.thegeekdiary.com/centos-rhel-7-how-to-rename-the-volume-group-for-root-and-swap/](https://www.thegeekdiary.com/centos-rhel-7-how-to-rename-the-volume-group-for-root-and-swap/)
#### view
```
lsblk -o NAME,FSTYPE,LABEL,MOUNTPOINT,SIZE
```
### MBR
#### add new hdd without rebooting
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
#### expand curent hdd without rebooting
```
ls -l /sys/class/scsi_device/
echo 1 > /sys/class/scsi_device/0\:0\:0\:0/device/rescan
  ||
ls -l /sys/class/block
echo 1 > /sys/class/block/sdX/device/rescan
```
#### expand LVM PV
```
pvresize /dev/sdX
```
### GPT
```
gdisk /dev/sdX
i -> save "Partition GUID code (Linux LVM)" and "Partition unique GUID"
d -> n -> insert Partition GUID code (Linux LVM) -> x -> c -> insert "Partition unique GUID"
partprobe
```
#### expand LVM PV
```
pvresize /dev/sdX
```
