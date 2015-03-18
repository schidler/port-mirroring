

# What is this "port mirroring"? #
"Port mirroring"(SPAN) is required for a network sniffer to analysis a network. In case your switch/router does not have port mirroring feature, you can use this software solution to monitor network traffic.

This "port-mirroring" program can mirror traffic of adapter(s) to another adapter or to a remote computer.  It is designed for openwrt and ddwrt, it also works in other linux systems.

Different to the "TEE" target of iptables, "port-mirroring" encapsulates a whole packet including ethernet headers using TSZP protocol http://en.wikipedia.org/wiki/TZSP.

"TEE" format mirroring is added in version 1.3. Since version 1.3, you can choose "TEE" or "TZSP" as the mirroring protocol.
## "TEE" or "TZSP" ##
"TEE" is an iptables routing target. It allows you to forward packets to another host. Iptables simply modifies the ethernet header of the original packets to the target host's MAC address. Therefore, you can not get the original source and destination mac addresses. Also, the target host must be in the same subnet as the mirroring source.

"TZSP" encapsulates a whole packet and forwards packets in udp protocol. Therefore the target host does not need to be in a same subnet. However, since a "TZSP" header is appended in each packet, your sniffer shall be able to strip "TZSP" headers to get the original packets. As I know, wireshark can do this.
# How to use it? #
## Steps ##

Following steps requires you to have an openwrt or ddwrt router. Don't have an openwrt or ddwrt router? check this url: http://wiki.openwrt.org/doc/start

  * Determine your router's OS version:
```
root@OpenWrt:~# cat /etc/banner
 ...
 Backfire (10.03.1, r29592) ------------------------
```
  * Determine your router's chip-set:
```
root@OpenWrt:~# cat /proc/cpuinfo
system type             : Broadcom BCM47XX
processor               : 0
cpu model               : Broadcom BCM3302 V2.9
```
  * Install a prebuilt port-mirroring package, you also can build the package from source.
```
#opkg update
#opkg install http://port-mirroring.googlecode.com/files/port-mirroring_1.2-1_OSVERSION_CHIPSET.ipk
```
  * Edit /etc/config/port-mirroring
```
config 'port-mirroring'
       option "target" '192.168.1.20'      #Target remote ip address or another interface
       option 'source_ports' 'wlan0'       #Source mirrored interfaces
       option 'filter' ''                    #libpcap filter
       option 'protocol' 'TEE'             #TEE(default) or TZSP
```
  * Start port-mirroring with debug
```
root@OpenWrt:~# port-mirroring --debug
```
  * Start port-mirroring as a daemon
```
root@OpenWrt:~# /etc/init.d/port_mirroring start
```
  * Stop port-mirroring
```
root@OpenWrt:~# /etc/init.d/port_mirroring stop
```
  * Remove port-mirroring
```
root@OpenWrt:~# opkg remove port-mirroring
```

# Typical Usage #
Suppose you have a linksys WRT54G router running openwrt backfire, now you want to monitor and filter client computers internet traffic.
The steps:
  * Install from prebuilt packages
```
#opkg update
#opkg install http://port-mirroring.googlecode.com/files/port-mirroring_1.2-1_backfire_brcm47xx.ipk
```
  * Edit mirroring settings
  * Start port-mirroring
  * Install an internet filtering program(eg: WFilter) in the remote computer to monitor and filter client computers.

# Build port-mirroring from source #
In case you can not find a prebuilt package, you can build you own. Steps:
  * Install OpenWrt Buildroot and its prerequisites. http://wiki.openwrt.org/doc/howto/buildroot.exigence
  * Check out openwrt source.
  * Create directory package/port-mirroring
  * Download and save the Makefile file into this directory from "Downloads".
  * Make menuconfig
  * Make