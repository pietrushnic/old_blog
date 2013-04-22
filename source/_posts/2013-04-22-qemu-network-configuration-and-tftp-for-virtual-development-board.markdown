---
layout: post
title: "Qemu network configuration and tftp for Virtual Development Board"
date: 2013-04-22 10:36
comments: true
categories: [embedded, linux, vdb]
published: false
keywords:
description:
---

This was not trivial task to me. As usual 'google is your friend' and RTFM works.
#### Setup tftpd ####
First install:
```bash
sudo apt-get install tftpd xinetd
```
Make sure that `/srv/tftp` is writable for your user. If directory doesn't exist 
create it and give needed privileges. If you want to change some 
server options edit `/etc/inetd.conf`. Copy or link our kernel to tftp server
directory.
```bash
cd /path/to/kernel/arch/arm/boot
ln -s $PWD/uImage /srv/tftp/kernel
```
Verify if everything works correctly:
```bash
cd             # go to home or any other directory different than arch/arm/boot
tftp 127.0.0.1 # connect to localhost tftp server
get kernel     # get kernel file
q              # quit tftp
```
Check if kernel file is in current directory. If yes than you tftp server is 
configured correctly, if not then google or ask me a question in comments 
section.

#### QEMU networking ###
I mixed [this](http://toast.djw.org.uk/qemu.html) BKM and few other resources
that I found in the net. Setting up network depend a lot on your configuration.
I will briefly describe my situation. It is quite possible that this won't fit 
yours.

I've eth0 with 10.0.2.15. What I want to do is create another interface tap0 and
bridge that connect eth0 and tap0. To do this I need few things:

* `brctl` is provided in Debian by `bridge-utils`
```
sudo apt-get install bridge-utils
```
* check if TUN module was installed
```
grep CONFIG_TUN= /boot/config-`uname -r`
```
you should get `y` or `m`, if it is `m` than modprobe tun:
```
sudo modprobe tun
```
* create tun device
```
sudo mknod /dev/net/tun c 10 200
```
* update `/etc/network/interfaces`:
```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# add br0 configuration
auto br0
iface br0 inet dhcp
bridge_ports eth0 # do not forget to attach eth0 to br0
bridge_fd 9
bridge_hello 2
bridge_maxage 12
bridge_stp off

# The primary network interface
# allow-hotplug eth0     # comment this
# iface eth0 inet dhcp   # and this
```
* use `/etc/qemu-ifup` script to bring up your network:
```bash
#!/bin/sh

echo "Executing /etc/qemu-ifup"
echo "Bringing up $1 for bridged mode..."
sudo /sbin/ifconfig $1 0.0.0.0 promisc up
echo "Adding $1 to br0..."
sudo /usr/sbin/brctl addif br0 $1
sleep 2
```

This should prepare you enviroment for tftpbooting in qemu.

#### Verify Qemu with tftp ####
_TODO_: improve this section with dhcp

Download simple arm image from qemu site and unpack it:
```bash
wget http://wiki.qemu.org/download/arm-test-0.2.tar.gz
tar xf arm-test-0.2.tar.gz
cd arm-test
```
Run your qemu with network and verify if you can connect to tftp service.
```bash
sudo qemu-system-arm -kernel zImage.integrator -net nic,vlan=0 -net tap,vlan=0,ifname=tap0,script=/etc/qemu-ifup -initrd arm_root.img -nographic -append "console=ttyAMA0"
```
Login using root. Interface address is probably set incorrectly to 10.0.2.15 
(same what br0). We need to change it for test purposes and run BusyBox tftp 
get:
```bash
ifconfig eth0 10.0.2.16
tftp -g -r kernel 10.0.2.15
ls -l kernel
```
If everything went ok you should have your kernel on virtual machine.


