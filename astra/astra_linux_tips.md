### cat /etc/apt/sources.list
```
deb https://download.astralinux.ru/astra/stable/orel/repository/ orel main contrib non-free
deb http://mirror.yandex.ru/astra/stable/orel/repository/ orel main contrib non-free
deb https://mirror.yandex.ru/debian/ stretch main contrib non-free
deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main
```
### GPG key install
``` sh
apt install debian-archive-keyring
apt install debian-keyring
```

### Auto configure network interface
allow-hotplug eth0
iface eth0 inet dhcp

### Install packages
`apt install vim htop atop mc fail2ban git -y`
#### rkhunter 1.4.6
`git clone https://salsa.debian.org/pkg-security-team/rkhunter.git`
