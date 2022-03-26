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
### .Net Core
[Диспетчер пакетов Debian 9 — установка .NET Core](https://docs.microsoft.com/ru-ru/dotnet/core/install/linux-package-manager-debian9)
### vim settings
#### disable visual mode at right click
```
touch ~/.vimrc
echo "set mouse-=a" > ~/.vimrc
source ~/.vimrc
```

### prometheus exporter
#### node-exporter
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
tar -zxvf node_exporter-1.3.1.linux-amd64.tar.gz
sudo mv node_exporter-1.3.1.linux-amd64/node_exporter /usr/local/bin/
sudo useradd -rs /bin/false node_exporter
sudo sh -c 'cat << EOF >> /etc/systemd/system/node_exporter.service
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
EOF'

sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter

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
## KESL
##### memory limit
1. Stop Kaspersky Endpoint Security ```systemctl stop kesl```
2. In the /var/opt/kaspersky/kesl/common/kesl.ini file, add the following parameter in the [General] section:
ScanMemoryLimit=<memory usage limit in megabytes>
3. Start Kaspersky Endpoint Security ```systemctl start kesl```
##### add key/code
kesl-control --add-active-key <path to key file>|<activation code>
