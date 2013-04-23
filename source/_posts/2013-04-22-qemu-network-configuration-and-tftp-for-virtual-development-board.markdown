---
layout: post
title: "0x5: Qemu network configuration and tftp for Virtual Development Board"
date: 2013-04-22 10:36
comments: true
categories: [embedded, linux, vdb]
published: false
keywords:
description:
---
## Table of contents ##

* [Introduction](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board/#intro)
* [Setup tftpd](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board/#setup-tftpd)
* [QEMU networking](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board/#qemu-networking)
* [Verify all components of Virtual Development Platform](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board/#verify-qemu-with-tftp)
* [What next ?](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board/#what-next)


<a id="intro"></a>
### Introduction ###
This was not trivial task to me. As usual `google is your friend` and `RTFM` works.
First we will set tftp which we use to download modified kernel for U-Boot.
Second I will show how to setup bridged network for QEMU needs and finally we
will perform some basic test of our setup. Let's go.

<a id="setup-tftpd"></a>
### Setup tftpd ###
First install:
```bash
sudo apt-get install tftpd
```
Make sure that `/srv/tftp` is writable for your user. If directory doesn't exist 
create it and give needed privileges. If you want to change some server options 
edit `/etc/inetd.conf`. Copy or link our kernel to tftp server
directory.
```bash
cd /path/to/kernel/arch/arm/boot
ln -s $PWD/uImage /srv/tftp/uImage
```
Verify if everything works correctly:
```bash
cd             # go to home or any other directory different than arch/arm/boot
tftp 127.0.0.1 # connect to localhost tftp server
get uImage     # get kernel file
q              # quit tftp
```
Check if kernel file is in current directory. If yes than you tftp server is 
configured correctly, if not then google or ask me a question in comments 
section.

<a id="qemu-networking"></a>
### QEMU networking ###
I mixed [this](http://toast.djw.org.uk/qemu.html) BKM and few other resources
that I found in the net. Setting up network depend a lot on your configuration.
I will briefly describe my situation. It is quite possible that this won't fit 
yours.

I've `eth0` with ip `10.0.2.15`. What I want to do is create another interface `tap0` and
bridge `br0` that will connect `eth0` and `tap0`. To do this I need few things:

* `brctl` is provided in Debian by `bridge-utils`
```
sudo apt-get install bridge-utils
```
* check if TUN module was installed
```
grep CONFIG_TUN= /boot/config-`uname -r`
```
you should get `y` or `m`, if it is `m` than `modprobe tun`:
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
sudo /sbin/brctl addif br0 $1
sleep 2
```

Restart networking services locally:
```
sudo service networking restart
```

This should prepare you environment for tftp booting in qemu.

<a id="verify-qemu-with-tftp"></a>
#### Verify all components of Virtual Development Platform ####

So right now we should have built [kernel uImage](/blog/2013/04/22/linux-kernel-for-embedded-system), [U-Boot image](/blog/2013/04/22/embedded-board-bootloader),
[configured qemu network](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board/#qemu-networking) and [tftp server](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board/#setup-tftpd). With all this components we can 
verify if our kernel booting on emulated `versatilepb`.

Run your qemu with network using U-Boot image as a kernel.
```bash
sudo qemu-system-arm -kernel /path/to/u-boot/u-boot -net nic,vlan=0 -net tap,vlan=0,ifname=tap0,script=/etc/qemu-ifup -nographic -M versatilepb
```
_NOTE_: We want to use u-boot file instead of u-boot.bin. First is ELF binary 
image and second is raw binary. Raw binary image can be used with `-bios`
parameter for qemu. If you try to give raw binary as a kernel parameter it will result with error:
```
qemu: fatal: Trying to execute code outside RAM or ROM at 0x08000000
```
_NOTE 2_: We have to specify `versatilepb` machine. If we forget it we will get 
error:
```
qemu: hardware error: integratorcm_read: Unimplemented offset 0x1e0000
```

Right now we have u-boot booted. Let's set ip addresses to boot uImage from our 
tftp server. For verification needs we don't want to `autoload` downloaded 
image, so we disable this through environment variable.
```bash
setenv autoload no
setenv ipaddr 10.0.2.16
setenv serverip 10.0.2.15
setenv bootfile uImage
tftpboot
```
Set addresses according to your configuration. For some reason I was unable to 
use u-boot `dhcp` feature. It assign me address that exist in the network.

We can take a close look on out downloaded image with `iminfo` command. 
`tftpboot` and `iminfo` should looks like that:
```
VersatilePB # tftpboot
SMC91111: PHY auto-negotiate timed out
SMC91111: MAC 52:54:00:12:34:56
Using SMC91111-0 device
TFTP from server 10.0.2.15; our IP address is 10.0.2.16
Filename 'uImage'.
Load address: 0x7fc0
Loading: #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         ##############################################
         0 Bytes/s
done
Bytes transferred = 
1895064 (1cea98 hex)
VersatilePB # iminfo

## Checking Image at 
00007fc0 ...
    Legacy image found
    Image Name:   Linux-3.9.0-rc8
    Image Type:   ARM Linux Kernel Image (uncompressed)
    Data Size:    1895000 Bytes = 1.8 MiB
    Load Address: 00008000
    Entry Point:  00008000
    Verifying Checksum ... OK
```

So, that what we want to see. Pretty new kernel `3.9.0-rc8` compiled as ARM 
image. We can try to boot it but we will end with kernel panic because lack of 
filesystem.

_NOTE 3_: If you want to see anything after booting this image with `bootm` you 
have to pass to kernel additional boot argument with serial device that should 
be used as a console. Before `bootm` set:
```
setenv bootargs console=ttyAMA0
```
You should get something similar to below log:
```
(...)
eth0: SMC91C11xFD (rev 1) at c89c8000 IRQ 57 [nowait]
eth0: Ethernet addr: 52:54:00:12:34:56
mousedev: PS/2 mouse device common for all mice
TCP: cubic registered
NET: Registered protocol family 17
VFP support v0.3: implementor 41 architecture 1 part 10 variant 9 rev 0
VFS: Cannot open root device "(null)" or unknown-block(0,0): error -6
Please append a correct "root=" boot option; here are the available partitions:
1f00           65536 mtdblock0  (driver?)
Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)
[<c0018afc>] (unwind_backtrace+0x0/0xf0) from [<c027af8c>] (panic+0x80/0x1d0)
[<c027af8c>] (panic+0x80/0x1d0) from [<c0343c64>] (mount_block_root+0x1a0/0x258)
[<c0343c64>] (mount_block_root+0x1a0/0x258) from [<c0343f08>] (mount_root+0xf0/0x118)
[<c0343f08>] (mount_root+0xf0/0x118) from [<c0344090>] (prepare_namespace+0x160/0x1b4)
[<c0344090>] (prepare_namespace+0x160/0x1b4) from [<c03438ec>] (kernel_init_freeable+0x168/0x1ac)
[<c03438ec>] (kernel_init_freeable+0x168/0x1ac) from [<c027a074>] (kernel_init+0x8/0xe4)
[<c027a074>] (kernel_init+0x8/0xe4) from [<c0013df0>] (ret_from_fork+0x14/0x24)
```
This is expected result. 

<a id="what-next"></a>
### What next ?###
We happily built basic virtual development, what we need right now is some 
[initial filesystem](/blog/2013/04/22/root-file-system-for-embedded-system).

