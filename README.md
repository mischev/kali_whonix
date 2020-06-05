Setting up Kali Linux 2016.2 + Whonix Gateway in VirtualBox for anonymous penetration testing
------------------------------------------------------------------------

## Configuring network ##
Remove network-manager:

    root@kali:~# apt-get remove network-manager
Open /interfaces:

    root@kali:~# nano /etc/network/interfaces
And add this strings:

>     auto eth0
>     iface eth0 inet static
>     address 10.152.152.12
>     netmask 255.255.192.0
>     gateway 10.152.152.10

Specify dns:

    root@kali:~# echo nameserver 10.152.152.10 > /etc/resolv.conf

----------
## Additional security tips ##
Disable TCP_timestamps

> Disabling this will hide current uptime of your machine.

    root@kali:~# echo “net.ipv4.tcp_timestamps = 0” > /etc/sysctl.d/tcp_timestamps.conf
Change time in UTC format:

    root@kali:~# ln -fs /usr/share/zoneinfo/Etc/UTC /etc/localtime
    root@kali:~# echo UTC0 > /etc/localtime
Reboot machine:

    root@kali:~# reboot

----------
## Downloading and configuring Tor Browser ##
Download tor browser (https://www.torproject.org/)
Enable tor for root user:

vim ./tor-browser_en-US/Browser/start-tor-browser
Find and comment this strings:
>     #if [ "`id -u`" -eq 0 ]; then
>     #       complain "The Tor Browser Bundle should not be run as root.  Exiting."
>     #       exit 1
>     #fi

----------
Let's have tor connect over whonix-gw instead of local tor, which would make a tor over tor connection. 

root@kali:~# apt-get install rinetd
root@kali:~# vim /etc/rinetd.conf

# SocksPorts
## Tor's default port
127.0.0.1        9050      10.152.152.10    9050
## Tor Browser Bundle's default port
127.0.0.1        9150      10.152.152.10    9150
## TorChat's default port
127.0.0.1        11109     10.152.152.10    9119
## Tor Messenger's default port
127.0.0.1        9152      10.152.152.10    9152
    
# ControlPorts
## Control Port Filter Proxy is running on Gateway's Port 9052
## Tor's default port
127.0.0.1        9051      10.152.152.10    9052
## Tor Browser Bundle's default port
127.0.0.1        9151      10.152.152.10    9052
## Tor Messenger's default port
127.0.0.1        9153      10.152.152.10    9052

vim ~/.bashrc

>     export TOR_SKIP_LAUNCH=1
>     export TOR_SKIP_CONTROLPORTTEST=1
>     export TOR_NO_DISPLAY_NETWORK_SETTINGS=1
>     export TOR_CONTROL_HOST="127.0.0.1"
>     export TOR_CONTROL_PORT="9151"

Done. No more Tor over Tor

# Change tor browser preferences

in /opt/torbrowser/Browser/defaults/pref add:

## Disable certificate pinning
pref("security.cert_pinning.enforcement_level", "0");

## Allow import of certificates
pref("security.nocertdb", "false");


----------
## Stream Isolation ##
Install torsocks:

apt install torsocks

torsocks ssh -D 2222 root@lsakdjflaksjdflksd.onion
