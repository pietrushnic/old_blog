---
layout: post
title: "Virtual enviroment for embedded development based on qemu"
date: 2013-03-31 01:27
comments: true
categories: 
published: false
keywords:
description:
---

### Intro ###
There are many reasons why ambitious programmers should care about huge rush in 
embedded market. IMO there is great potential in it. I don't want to go deep in 
to this but take a look at all tablets, phones, smart tvs and other cool stuff 
like wireless in you washing machine or refrigerator (ok this two are right now only a gadgets).
This is all embedded. Wages of embedded developers are really high. 

This environment is very interesting. A lot to learn, a lot to do, so why not get 
your hands dirty ?

First question: How to start ? To do real embedded development, to learn the 
most, you simply buy hardware like all this `${animal}board (animal={panda,beagle,hawk})`.
This is the best way but if you're beginner (like me) you probably don't want to 
spend $50 for stuff that you throw away after month because you don't like to 
deal with it or problems are not on your level, than maybe you should take look around and wonder if it is
possible to build virtual environment to check what kind of problem you may to
encounter using embedded ? IMO this is where world going: proof of concept embedded
platforms affordable for common man, but also testing environment based on virtualized solution,
to check basic features, speed up testing and development.

I realize that it is achievable and I will learn a lot trying to implement this idea. Finally I was convinced reading 
[this](http://www.elinux.org/Virtual_Development_Board) two years old article on [eLinux.org](http://www.elinux.org).
So my article will be finished and up to date for a while.

Here I will try to share my knowledge about virtual development board configuration.

### Qemu ####
This is our virtual platform. As usually I will start with latest greatest version from git:
```bash
git clone http://git.qemu.org/git/qemu.git
```
Compile it and install. Right now I will use only `arm-softmmu` target because it 
will emulate whole arm system for me. ARM right now dominated big part of 
embedded market but we will see if situation won't change in feature.
```bash
./configure --target-list=arm-softmmu
make
make install
```
During configuration process you can encounter lack of `pixman`, just accept qemu 
offer to initialize it as a submodule. After that first component is ready.
Right now we can emulate our ARM based board with many types of CPUs. List of 
all available can be retrieved by running command `qemu-system-arm -cpu help`


### Toolchain ###
This is probably the most complicated topic of all but we need to deal with it 
at the beginning. I read a lot about toolchains but still not know enough to 
explain details. I think that best answers are in crosstool-ng [documentation](http://crosstool-ng.org/hg/crosstool-ng/file/0fc56e62cecf/docs).
I will not deal with preparing toolchain in this document because procedure for 
creating it takes pretty long. So you get two options:

* read my article about [arm-unknown-linux-gnueabi](/blog/2013/03/30/yet-another-quick-build-of-arm-unknown-linux-gnueabi)
* or install toolchain ready to use like [Emdebian](http://www.emdebian.org/)

If you follow my path with building own toolchain using crosstool-ng than skip
to Bootloader section. If not then you should install your Emdebian toolchain.

Add below lines to you `/etc/apt/sources.list`:
```bash
deb http://ftp.uk.debian.org/emdebian/toolchains unstable main
```
Install Emdebian keys and update:
```bash
sudo apt-get install emdebian-archive-keyring
sudo apt-get update
sudo apt-get install gcc-4.7-arm-linux-gnueabi
```
Check where arm-linux-eabi-gcc-4.7 was installed:
```bash
pietrushnic@lothlann:~/src/linux-3.9-rc4$ whereis arm-linux-gnueabi-gcc-4.7
arm-linux-gnueabi-gcc-4: /usr/bin/arm-linux-gnueabi-gcc-4.7 /usr/bin/X11/arm-linux-gnueabi-gcc-4.7
```
And link it to arm-linux-gnueabi-gcc:
```bash
sudo ln -s /usr/bin/arm-linux-gnueabi-gcc-4.7 /usr/bin/arm-linux-gnueabi-gcc
```

Toolchain is ready to use.
_Note_: I tried CodeSourcery toolchain `arm-2012.09-64-arm-none-linux-gnueabi.bin`,
but it contain binutils defect that not allow correctly build kernel. If you see
something like this in log:
```
Error: selected processor does not support ARM mode `ldralt lr,[r1],#4'
```
That means you experience same thing, please use Emdebian or Linaro toolchain.

### Bootloader ###

It is a program written to bring up more complex code - kernel. On very simple system 
it can even not exist - our ARM system is not so simple. Well known fact is 
what bootloader should do, it should prepare whole environment like memory and 
all stuff that kernel will need at its start point. There are many approaches to 
this problem. The most popular today is [U-Boot](http://www.denx.de/wiki/U-Boot)
but there are alternatives like [The Barebox Bootloader](http://www.barebox.org/).
I will try to get to know them better in future. Right now we will use U-Boot
as our bootloader to make the edit-compile-download-test cycle faster. So get 
the code:
```bash
git clone http://git.denx.de/u-boot.git
```
U-Boot configuration depends on pair cpu-board. So right now we know that our cpu 
will be some ARM but what exactly ? It depends on configuration we will use. In 
most scenarios presented in Internet the versatilepb was used. We can also 
think about running different boards. To get machines supported by our brand new 
qemu just type `qemu-system-arm -M help`

You may ask what is versatilepb ? According to [this page](http://www.arm.com/products/tools/development-boards/versatile/index.php)
versatile is highly modular, feature-rich range of development board. `pb`
means `Platform Baseboard` integrated, standalone development system with fixed 
CPU. But this is only corporate babble :)

Let's use it (for crosstool-ng toolchain):
```bash
make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi- versatilepb_config
make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi- -s
```
for Emdebian toolchain:
```bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- versatilepb_defconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- -s
```

If you wonder what is the convention for cross compiler name:
```
[arch]-[vendor]-[os]-[abi]
```

### Embedded linux ###
But to deal with our _embedded_ board we need also operating system or some 
kind of software that will allow us to use this board features. There are many 
different solutions but we will use the simplest one. To boot system we need at 
least kernel. So we have to prepare kernel for versatilepb. 

Unfortunately I was unable to compile latest code from kernel.org (3.9-4rc). I 
get this error:
```
arm-linux-gnueabi-ld: cannot find usr/built-in.o: No such file or directory
```
Right now I don't have time to investigate it but maybe this is only temporary 
regression.

So get stable 3.8.5 :(
```
wget https://www.kernel.org/pub/linux/kernel/v3.x/linux-3.8.5.tar.xz
```
and do same thing as for U-boot. For crosstool-ng toolchain (note that config is different):
```bash
make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi- versatile_defconfig
make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi- uImage
```
for Emdebian:
```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- versatile_defconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- uImage
```
We have kernel. How we can provide this kernel to our development environment ?
So probably the best way is use bootloader as it was showed on eLinux.org.

### TFTP and network configuration ###

This was not trivial task to me. As usual 'google is your friend'  and RTFM works.
#### Setup tftpd ####
First install:
```bash
sudo apt-get install tftpd
```
Make sure that `/srv/tftp` is writable for your user. If you want to change some 
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

* `brctl` is provided in Debian bu `bridge-utils`
```bash
sudo apt-get install bridge-utils
```
* check if TUN module was installed
```bash
grep CONFIG_TUN= /boot/config-`uname -r`
```
you should get `y` or `m`, if it is `m` than modprobe tun:
```bash
sudo modprobe tun
```
* create tun device
```bash
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
