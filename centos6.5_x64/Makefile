# Port numbers were randomly chosen so change them if you wish, but 
# be sure to change the corresponding lines in IPTABLES_CONFIG.
define BT_SYNC_CONFIG
{
	"listening_port" : 45712,
	"check_for_updates" : false,
	"use_upnp" : false,
	"webui" :
	{
		"listen" : "0.0.0.0:8888" // Comment this out to disable the web ui
	}
}
endef

# If you change port numbers, be sure to change the corresponding 
# lines in the BT_SYNC_CONFIG. Port 45712 is for BitTorrent, and
# 8888 is for the BTSync web interface.
define IPTABLES_CONFIG
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 45712 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8888 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
endef

define BT_SYNC_SUPERVISORD
[program:btsync]
command=sudo -u www-data /usr/local/btsync/btsync --config /etc/btsync/sync.conf
autostart=true
autorestart=true
redirect_stderr=true
stdout_logfile=/var/log/supervisor/%(program_name)s.log
stderr_logfile=/var/log/supervisor/%(program_name)s_error.log
endef

export BT_SYNC_CONFIG IPTABLES_CONFIG BT_SYNC_SUPERVISORD

# This is the default setup routine. It will update the server and add the 
# Fedora EPEL packages. It will then configure the iptables firewall and 
# install fail2ban for some added security against brute force attacks. 
# Finally, it will install BTSync and then reboot to make sure everything 
# comes up properly after a restart.
.PHONY: all
all: update epel firewall fail2ban cust setuser btsync clean reboot

# If you object to installing EPEL, you can run a less opinionated routine 
# with 'make base' instead of 'make'.
.PHONY: base
base: update firewall cust setuser btsync clean

.PHONY: update
update:
	yum -y update

.PHONY: epel
epel:
	curl -L http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm > epel-release-6-8.noarch.rpm
	yum -y install epel-release-6-8.noarch.rpm
	yum -y update

.PHONY: firewall
firewall:
	echo "$$IPTABLES_CONFIG" > /etc/sysconfig/iptables
	chkconfig iptables on

.PHONY: fail2ban
fail2ban:
	yum -y install fail2ban
	cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
	chkconfig fail2ban on
	
.PHONY: cust
cust:
	yum -y install tar sudo
	
.PHONY: setuser
setuser:
	useradd www-data -u 33 -s /sbin/nologin -g tape -d /home/www-data
	mkdir -p /usr/local/btsync
	chown -R 33:33 /usr/local/btsync

.PHONY: btsync
btsync:
	curl -L http://syncapp.bittorrent.com/1.4.111/btsync_x64-1.4.111.tar.gz > btsync.tar.gz
	tar -zxvf btsync.tar.gz
	mv btsync /usr/local/btsync/
	chown 33:33 /usr/local/btsync/btsync
	mkdir -p /etc/btsync
	echo "$$BT_SYNC_CONFIG" > /etc/btsync/sync.conf
	echo "$$BT_SYNC_SUPERVISORD" > /etc/supervisor/conf.d/sync.conf

.PHONY: clean
clean:
	rm -f LICENSE.TXT btsync.tar epel-release-6-8.noarch.rpm Makefile

.PHONY: reboot
reboot:
	reboot now
