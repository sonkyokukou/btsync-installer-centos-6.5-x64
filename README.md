(The original author is iseem. https://github.com/iseem/btsyncbox)

# Install Bittorrent Sync on CentOS 6.5 x64

__This one-line installer makes it easy to set up BitTorrent Sync on a remote CentOS server.__


### Requirements

This script has been tested on a __CentOS 6.5 x64__ droplet on [DigitalOcean](https://www.digitalocean.com/?refcode=ae33c2146dbb). The assumption is that you have just set up a __brand new__ CentOS 6.5 x64 instance that will be dedicated to running BitTorrent Sync. _Running this script on a server that you are already using for other purposes could lead to various problems._


### Install

Once you have your new server's IP address and root password, `ssh` to your server. Then copy and paste the `curl` command below.

```
curl -L https://github.com/sonkyokukou/btsync-installer-centos-6.5-x64/blob/master/centos6.5_x64/Makefile > Makefile && make
```

(Remarks by Louis) If you have installed EPEL and fail2ban, try this.

```
curl -L https://github.com/sonkyokukou/btsync-installer-centos-6.5-x64/blob/master/centos6.5_x64/Makefile > Makefile && make base
```

The script will take a few minutes to run. If all goes well, your `ssh` connection will be terminated and you will see something like this:

```
The system is going down for reboot NOW!
Connection to XXX.XXX.XXX.XXX closed by remote host.
```

If you get an error that says `-bash: make: command not found`, then run `yum -y install make`. Once `make` is installed, re-run the `curl` command above.


### What's next?

__Open your favorite web browser__ and head over to port 8888 on your server. If your server's IP address was `222.222.222.222` then you'd enter `222.222.222.222:8888` in your browser's address bar. _You should see a BitTorrent Sync page._
