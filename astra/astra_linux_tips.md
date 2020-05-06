### cat /etc/apt/sources.list
```
deb https://download.astralinux.ru/astra/stable/orel/repository/ orel main contrib non-free
deb http://mirror.yandex.ru/astra/stable/orel/repository/ orel main contrib non-free
deb https://mirror.yandex.ru/debian/ stretch main contrib non-free
deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main
```
### GPG keys install
```
apt install debian-archive-keyring debian-keyring -y
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
`apt install vim git htop atop fail2ban mc iptables-persistent byobu -y`
#### rkhunter latest
```
cd /opt && git clone https://salsa.debian.org/pkg-security-team/rkhunter.git
./installer.sh --install
```
#### enable byobu
`byobu-enable`
#### configure fail2ban & iptables
`mkdir /var/log/{fail2ban,iptables}`
##### change log folder at
```
/etc/fail2ban/fail2ban.conf 
&&
systemctl restart fail2ban
```
##### create /etc/rsyslog.d/iptables.conf:
```
:msg, contains, "IPTABLES DENIED" -/var/log/iptables/iptables.log
& ~
```
##### restart rsyslog:
```systemctl restart rsyslog.service```
