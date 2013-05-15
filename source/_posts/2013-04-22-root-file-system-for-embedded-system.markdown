---
layout: post
title: "0x6: Root file system for embedded system"
date: 2013-04-22 10:40
comments: true
categories: [embedded, linux, vdb]
published: false
keywords:
description:
---
## Table of contents ##

* [Introduction](/blog/2013/04/22/root-file-system-for-embedded-system/#intro)
* [Get and build BusyBox](/blog/2013/04/22/root-file-system-for-embedded-system/#get-bb)
* [Fast and simple](/blog/2013/04/22/root-file-system-for-embedded-system/#fast-and-simple)
* [](/blog/2013/04/22/root-file-system-for-embedded-system/#intro)
* [](/blog/2013/04/22/root-file-system-for-embedded-system/#intro)

<a id="intro"></a>
### Introduction ###
To make our embedded linux work as virtual development platform we need some 
environment after booting. There is many approaches to get working root file 
system but I will use the easiest one as a exercise. I don't want to create full 
embedded distribution (this is good plan for future works). Right now I will be 
happy with simple initramfs based on [BusyBox](http://busybox.net/).

For all interested in creating own root filesystem there are few places where 
you can find informations:

* [Embedded Linux: Small Root Filesystems](http://lwn.net/Articles/210046/)
* [ramfs-rootfs-initramfs](https://www.kernel.org/doc/Documentation/filesystems/ramfs-rootfs-initramfs.txt)
* [Creating a Root File System for Linux on OMAP35x](http://processors.wiki.ti.com/index.php/Creating_a_Root_File_System_for_Linux_on_OMAP35x)

<a id="get-bb"></a>
### Get and build BusyBox ###
Clone git repository:
```
git clone git://git.busybox.net/busybox
```

<a id="fast-and-simple"></a>
### Fast and simple ###
Of course make sure to use correct toolchain. I made few notes about 
Ubuntu/Linaro toolchain in [previous post](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board/#ubuntu-issues)
```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- menuconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- install
cd _install/
find .| cpio -o --format=newc > ../rootfs.img
cd ..
gzip -c rootfs.img > rootfs.img.gz
```
