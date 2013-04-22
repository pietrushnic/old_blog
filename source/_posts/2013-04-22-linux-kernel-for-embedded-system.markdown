---
layout: post
title: "Linux kernel for embedded system"
date: 2013-04-22 10:33
comments: true
categories: [embedded, linux, vdb]
published: false
keywords:
description:
---
But to deal with our _embedded_ board we need also operating system or some 
kind of software that will allow us to use this board features. There are many 
different solutions but we will use the simplest one. To boot system we need at 
least kernel. So we have to prepare kernel for versatilepb. 

Let's start with cloning Linux repository:
```
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
```
and do same thing as for U-boot. For crosstool-ng toolchain (note that config is different):
```
cd linux
make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi- versatile_defconfig
```
for Emdebian:
```
cd linux
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- versatile_defconfig
```

It looks some feature is disabled versatile_defconfig. I mean CONFIG_AEABI this 
specifies file format, data types, register usage and other things. The main 
difference between EABI and ABI is that privileged instructions are allowed in 
application code. More about EABI [here](http://en.wikipedia.org/wiki/Application_binary_interface#EABI).
To enable this option mark:
```
Kernel Features -> Use the ARM EABI to compile the kernel
```

Crosstool-NG:
```
make ARCH=arm CROSS_COMPILE=arm-unknown-linux-gnueabi- uImage
```
Emdebian:
```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- uImage
```
We have kernel. How we can provide this kernel to our development environment ?
So probably the best way is use bootloader as it was showed on eLinux.org.

NOTE: You can encounter error like this:
```
(...)
  UIMAGE  arch/arm/boot/uImage
  "mkimage" command not found - U-Boot images will not be built
  make[1]: *** [arch/arm/boot/uImage] Error 1
  make: *** [uImage] Error 2
```
Of course it means that we need mkimage to create U-Boot image, so:
```
sudo apt-get install uboot-mkimage
```

