# smartctl-zabbix

Template and script for monitoring HDD S.M.A.R.T data from Zabbix on Linux OS
S.M.A.R.T. items description copied from Wikipedia (https://en.wikipedia.org/wiki/S.M.A.R.T.)


Installation for Ubuntu or other Linux
------------

Install smartctl:
sudo apt install smartmontools zabbix-sender

Copy the scripts, zabbix_agentd.conf.d into /etc/zabbix/

Copy sudoers.d into /etc/

Check arguments: Server, Hostname, ListenIP in zabbix_agentd.conf

Arch Linux package
------------------

```
$ cd archlinux
$ makepkg -s
```

Configuration
-------------

This template uses active checks, so you'll need to make some changes
to `/etc/zabbix/zabbix_agentd.conf`. Edit them to match your configuration:

```
# Include=/usr/local/etc/zabbix_agentd.userparams.conf
# Include=/usr/local/etc/zabbix_agentd.conf.d/
Include=/etc/zabbix/zabbix_agentd.conf.d/
# Include=/usr/local/etc/zabbix_agentd.conf.d/*.conf
...
Server=192.168.10.39,192.168.10.17
...
ServerActive=192.168.10.39
...
Hostname=Saturn
```

Make sure it works
------------------

Data source script:

```
$ sudo /etc/zabbix/scripts/zabbix_smartctl.sh
{"data":[
{ "{#DEVNAME}":"sda", "{#VISNAME}":"sda", "{#DEVTYPE}":"sat" }
]}
```

```
$ sudo /etc/zabbix/scripts/zabbix_smartctl sda sat
PASSED
 smartctl.info[sda,sat,model_family] "Western Digital Blue"
 smartctl.info[sda,sat,device_model] "WDC WD5000AAKX-00ERMA0"
 smartctl.info[sda,sat,serial_number] "WD-WCC2EJA56447"
 smartctl.info[sda,sat,firmware_version] "15.01H15"
 smartctl.info[sda,sat,user_capacity] "500,106,780,160 bytes [500 GB]"
 smartctl.info[sda,sat,sector_size] "512 bytes logical/physical"
 smartctl.smart[sda,sat,1,value] 200
 smartctl.smart[sda,sat,1,worst] 200
 smartctl.smart[sda,sat,1,thresh] 051
 smartctl.smart[sda,sat,1,raw_value] 0
 smartctl.smart[sda,sat,3,value] 139
 smartctl.smart[sda,sat,3,worst] 137
 smartctl.smart[sda,sat,3,thresh] 021
 smartctl.smart[sda,sat,3,raw_value] 4050
...
 smartctl.smart[sda,sat,200,value] 200
 smartctl.smart[sda,sat,200,worst] 200
 smartctl.smart[sda,sat,200,thresh] 000
 smartctl.smart[sda,sat,200,raw_value] 0
```

Access permissions:

```
$ sudo -u zabbix-agent /etc/zabbix/scripts/zabbix_smartctl.sh
{"data":[
{ "{#DEVNAME}":"sda", "{#VISNAME}":"sda", "{#DEVTYPE}":"sat" }
]}
```
if ```/usr/sbin/smartctl --scan-open``` list empty output, then create /etc/zabbix/smartctl-devices.json

```
{"data":[
{ "{#DEVNAME}":"sda", "{#VISNAME}":"sda", "{#DEVTYPE}":"cciss,0" }
,{ "{#DEVNAME}":"sda", "{#VISNAME}":"sda", "{#DEVTYPE}":"cciss,1" }
,{ "{#DEVNAME}":"sda", "{#VISNAME}":"sda", "{#DEVTYPE}":"cciss,2" }
,{ "{#DEVNAME}":"sda", "{#VISNAME}":"sda", "{#DEVTYPE}":"cciss,3" }
]}
```

Make sure to restart the Zabbix agent daemon:

```
$ sudo systemctl restart zabbix-agent
```

As usual, you'll need to import Zabbix template and attach it to target hosts. Wait a while for disk discovery to kick in (or use force refresh), then wait a bit more for the actual data collection to start.

Finally in zabbix setup the discovery rule and related items you need.
