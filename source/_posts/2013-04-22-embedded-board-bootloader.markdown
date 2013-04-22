---
layout: post
title: "0x3: Virtual Development Board"
date: 2013-04-22 10:32
comments: true
categories: [embedded, linux, vdb]
published: false
keywords:
description:
---
## Table of contents ##

1. [Intro to Virtual Development Board building](/blog/2013/04/22/intro-to-virtual-development-board-building)
2. [Qemu as an environment for embedded board emulation](/blog/2013/04/22/qemu-as-an-environment-for-embedded-board-emulation)
3. [Toolchain for Virtual Development Board](/blog/2013/04/22/toolchain-for-virtual-development-board)
4. [-> Embedded board bootloader <-](/blog/2013/04/22/embedded-board-bootloader)
5. [Linux kernel for embedded system](/blog/2013/04/22/linux-kernel-for-embedded-system)
6. [Qemu network configuration and tftp for Virtual Development Board](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board)
7. [Root file system for embedded system](/blog/04/22/root-file-system-for-embedded-system)
8. [Root file system over NFS]()
9. [Final booting and ideas for improvements]()

## Embedded board bootloader ##

## What is bootloader ? ##
It is a program written to bring up more complex code (eg. kernel). 

On very simple system it can even not exist - our ARM-based virtual development 
system is not so simple.  Bootloader should prepare all required hardware that 
kernel or different operating software will need at its start point. It is hard 
to create cross platform bootloader because of variety of system requirements.

## Why we need bootloader ? ##
In case of using QEMU as an emulation environment we could simply pass kernel 
and initramfs as parameters, but it is not common practice for real development environment. 
Usually bootloader is stripped in production environment where boot time is 
crucial. Second thing, bootloader is useful to learn how real development environment
for embedded system works. From other side using bootloader we can create single 
binary file that contain bootable embedded system, so we can run it without giving
multiple arguments at QEMU startup. I will try to keep in mind idea about 
being as close to real development environment as possible.

## Which bootloader ? ##
There are many approaches to this problem. The most popular today is 
[U-Boot](http://www.denx.de/wiki/U-Boot) but there are alternatives like [The Barebox Bootloader](http://www.barebox.org/).
I will try to get to know them better in future. Right now I will use U-Boot as
Virtual Development Board bootloader to make the edit-compile-download-test cycle faster. So get the code:
```bash
git clone http://git.denx.de/u-boot.git
```
U-Boot configuration depends on pair cpu-board. So right now we know that our 
cpu will be some ARM but what exactly ? It depends on configuration we will use.  
In most scenarios presented in Internet the `versatilepb` was used. We can also 
think about running different boards.

### What is versatilepb ? ###
According to [this page](http://www.arm.com/products/tools/development-boards/versatile/index.php)
versatile is highly modular, feature-rich range of development board. `pb`
means `Platform Baseboard` integrated, standalone development system with fixed 
CPU. But this is only corporate babble :)

## Compile ##
For `Emdebian` cross-toolchain:
```bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- versatilepb_defconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- -s
```

_Note_: If you wonder what is the convention for cross-toolchain prefix:
```
[arch]-[vendor]-[os]-[abi]
```
`Emdebian` striped vendor probably to keep name short, but for example 
`Corsstool-NG` follow convetion and calls toolchain like 
`arm-unknown-linux-gnueabi-`.

Kudos:

1. [Introduction to Das U-Boot, the universal open source bootloader](http://www.linuxfordevices.com/c/a/Linux-For-Devices-Articles/Introduction-to-Das-UBoot-the-universal-open-source-bootloader/)
2. [U-Boot](http://www.denx.de/wiki/U-Boot)
3. [U-boot for the multi-boot support](http://forum.xda-developers.com/showthread.php?t=2201146)
4. [Booting Linux with U-Boot on QEMU ARM](http://balau82.wordpress.com/2010/04/12/booting-linux-with-u-boot-on-qemu-arm/)


