### Add source at /etc/apt/sources.list
```
deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main
deb https://download.astralinux.ru/astra/stable/orel/repository/ orel main contrib non-free
deb http://mirror.yandex.ru/astra/stable/orel/repository/ orel main contrib non-free
deb https://mirror.yandex.ru/debian/ stretch main contrib non-free
```
### GPG keys install
```
apt install debian-archive-keyring debian-keyring dirmngr -y
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
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
### Rename VolumeGroup LVM
1. Reboot your machine and choose recovery mode (not mandatory but it’s better)
2. Run `vgrename`
```
vgrename actualName NewName
```
3. Edit `/etc/fstab` and change all the entries with the new name
4. Edit `/etc/initramfs-tools/conf.d/resume` and replace the old name with the new one
5. Rebuild an initramfs:
```
update-initramfs -u -k all
```
6. Reboot
7. In memory:
 * [https://www.thegeekdiary.com/centos-rhel-7-how-to-rename-the-volume-group-for-root-and-swap/](https://www.thegeekdiary.com/centos-rhel-7-how-to-rename-the-volume-group-for-root-and-swap/)
 ### LVM create LV partition at free space
lvcreate -n PARTITION_NAME -l 100 %FREE VG_NAME
