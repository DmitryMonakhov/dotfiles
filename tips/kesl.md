## KESL
##### memory limit
1. Stop Kaspersky Endpoint Security ```systemctl stop kesl```
2. In the /var/opt/kaspersky/kesl/common/kesl.ini file, add the following parameter in the [General] section:
ScanMemoryLimit=<memory usage limit in megabytes>
3. Start Kaspersky Endpoint Security ```systemctl start kesl```
##### add key/code
kesl-control --add-active-key <path to key file>|<activation code>
