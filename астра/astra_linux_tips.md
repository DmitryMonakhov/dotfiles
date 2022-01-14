### Add source at /etc/apt/sources.list
```
deb https://nginx.org/packages/debian/ stretch nginx
deb-src https://nginx.org/packages/debian/ stretch nginx
deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main
deb https://download.astralinux.ru/astra/stable/orel/repository/ orel main contrib non-free
deb http://mirror.yandex.ru/astra/stable/orel/repository/ orel main contrib non-free
deb https://mirror.yandex.ru/debian/ stretch main contrib non-free
```
### GPG keys install
```sh
sudo apt install debian-archive-keyring debian-keyring dirmngr dbus ntp open-vm-tools -y
sudo wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo wget https://nginx.org/keys/nginx_signing.key
sudo apt-key add nginx_signing.key
```
### Configure network interface
#### auto
```
allow-hotplug eth0
iface eth0 inet dhcp
```
#### static
```
# The primary network interface
allow-hotplug eth0
iface eth0 inet static
        address x.x.x.x
        netmask 255.255.255.0
        network x.x.x.x
        broadcast x.x.x.x
        gateway x.x.x.x
        dns-nameserver x.x.x.x
        dns-nameserver x.x.x.x
        dns-nameserver x.x.x.x
```
### Install packages
`apt install vim git htop atop fail2ban mc iptables-persistent byobu lsof ltrace strace -y`
#### rkhunter latest
```
cd /opt && git clone https://salsa.debian.org/pkg-security-team/rkhunter.git
./installer.sh --install
```
#### enable byobu
`byobu-enable`
#### configure fail2ban & iptables
`mkdir /var/log/{fail2ban,iptables,rkhunter}`
##### change log folder at
```
/etc/fail2ban/fail2ban.conf 
&&
systemctl restart fail2ban
```
##### change log folder at
```
/etc/rkhunter.conf
```
##### create /etc/rsyslog.d/iptables.conf
```
:msg, contains, "IPTABLES DENIED" -/var/log/iptables/iptables.log
& ~
```
##### restart rsyslog
```systemctl restart rsyslog.service```
##### configure logs rotate
cat /etc/logrotate.d/fail2ban:
```
/var/log/fail2ban/fail2ban.log {

    daily
    rotate 90
    compress

    delaycompress
    missingok
    postrotate
        fail2ban-client flushlogs 1>/dev/null
    endscript

    # If fail2ban runs as non-root it still needs to have write access
    # to logfiles.
    # create 640 fail2ban adm
    create 640 root adm
}
```
cat /etc/logrotate.d/iptables:
```
/var/log/iptables/iptables.log
{
        rotate 30
        daily
        missingok
        notifempty
        compress
        delaycompress
        sharedscripts
}
```
cat /etc/logrotate.d/rkhunter:
```
/var/log/rkhunter/rkhunter.log {
        daily
        missingok
        rotate 30
        compress
        delaycompress
        notifempty
        create 640 root adm
}
```
testing rotate: 
```
sudo logrotate -v -f /etc/logrotate.conf
```
### LVM
#### create LV partition at free space
```
lvcreate -n LV_NAME -l 100 %FREE VG_NAME
mkfs.ext4 /dev/mapper/LV_NAME
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
### .Net Core
[Диспетчер пакетов Debian 9 — установка .NET Core](https://docs.microsoft.com/ru-ru/dotnet/core/install/linux-package-manager-debian9)
### vim settings
#### disable visual mode at right click
```
touch ~/.vimrc
echo "set mouse-=a" > ~/.vimrc
source ~/.vimrc
```

### pgadmin4
Install the public key for the repository (if not done previously):
```
curl https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo apt-key add
```
#### Create the repository configuration file:
```
sudo sh -c 'echo "deb https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'
```
#### Install for both desktop and web modes:
```
sudo apt install pgadmin4
```
#### Install for desktop mode only:
```
sudo apt install pgadmin4-desktop
```
#### Install for web mode only: 
```
sudo apt install pgadmin4-web 
```
#### Configure the webserver, if you installed pgadmin4-web:
```
sudo /usr/pgadmin4/bin/setup-web.sh
```
### Sheduled reboot
```
echo "/sbin/shutdown -r now" |at 04:00 tomorrow
```
#### Schedule Examples
```
at 21:00 today
at 21:00 tomorrow
at 21:00 tuesday
at 21:00 July 11 (Month Day)
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
