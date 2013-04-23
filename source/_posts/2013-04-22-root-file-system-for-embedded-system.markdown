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
* [Introduction](/blog/2013/04/22/root-file-system-for-embedded-system/#intro)
* [Introduction](/blog/2013/04/22/root-file-system-for-embedded-system/#intro)
* [Introduction](/blog/2013/04/22/root-file-system-for-embedded-system/#intro)
* [Introduction](/blog/2013/04/22/root-file-system-for-embedded-system/#intro)

<a id="intro"></a>
### Introduction ###
To make our embedded linux work as virtual development platform we need some 
environment after booting. There is many approaches to get working root file 
system but I will use the easiest one as a exercise. I don't want to create full 
embedded distribution (this is good plan for future works). Right now I will be 
happy with simple initramfs based on [BusyBox]().

### Get and build BusyBox ###
Clone git repository:
```
git clone git://git.busybox.net/busybox
```

### Fast and simple ###
```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- menuconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- install
cd _install/
find .| cpio -o --format=newc > ../rootfs.img
cd ..
gzip -c rootfs.img > rootfs.img.gz
```
