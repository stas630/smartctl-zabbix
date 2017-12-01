# smartctl-zabbix

Template and script for monitoring HDD S.M.A.R.T data from Zabbix on Linux OS
S.M.A.R.T. items description copied from Wikipedia (https://en.wikipedia.org/wiki/S.M.A.R.T.)


Installation
------------

Install jq:
      sudo apt install smartmontools

Copy the scripts, zabbix_agentd.conf.d into /etc/zabbix/

Copy sudoers.d into /etc/

Check arguments: Server, Hostname, ListenIP in zabbix_agentd.conf

Finally in zabbix setup the discovery rule and related items you need.# smartctl-zabbix
