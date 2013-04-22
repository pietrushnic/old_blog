---
layout: post
title: "0x2: Virtual Development Board"
date: 2013-04-22 08:42
comments: true
categories: [embedded, linux, vdb]
published: false
keywords:
description:
---
## Table of contents ##

1. [Intro to Virtual Development Board building](/blog/2013/04/22/intro-to-virtual-development-board-building)
2. [Qemu as an environment for embedded board emulation](/blog/2013/04/22/qemu-as-an-environment-for-embedded-board-emulation)
3. [-> Toolchain for Virtual Development Board <-](/blog/2013/04/22/toolchain-for-virtual-development-board)
4. [Embedded board bootloader](/blog/2013/04/22/embedded-board-bootloader)
5. [Linux kernel for embedded system](/blog/2013/04/22/linux-kernel-for-embedded-system)
6. [Qemu network configuration and tftp for Virtual Development Board](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board)
7. [Root file system for embedded system](/blog/04/22/root-file-system-for-embedded-system)
8. [Root file system over NFS]()
9. [Final booting and ideas for improvements]()

## Toolchain for Virtual Development Board ##

This is probably the most complicated topic of all related to embedded 
development but we need to deal with it at the beginning. I read a lot about 
toolchains but still don't know enough to explain details. I think that best 
answers are in crosstool-ng [documentation](http://crosstool-ng.org/hg/crosstool-ng/file/0fc56e62cecf/docs).

### What is toolchain ? ###

_Toolchain_ as the name said is a set of tools chained together, so output of 
one tool is the input for different tool. This is well known concept in 
Linux (eg. pipes). In embedded environment toolchain is called cross-toolchain or 
cross-compiler, because usually it compiles on one architecture and generate 
code for another (eg. it compiles on x86 and generate code for arm).

### Why we need cross-toolchain ? ###

I suspect that your laptop/PC is not based on ARM processor, most probably it based on 
x86 architecture so you cannot simply compile code and run it in our 
virtual-arm-based environment. To prepare operating system and tools for it we 
need cross-toolchain.

### How to create toolchain ? ###

Process of creating cross-toolchain from scratch is not easy and takes some 
time. There are few other ways to get toolchain, than creating it from scratch.
First we can use prebuilt toolchain providers like: `CodeSourcery`, `Linaro`, `DENX 
EDLK` or `Emdebian`. Second we can create toolchain using special building system like: `Buildroot`, `Crosstool-NG` 
or `Bitbake`. I will not deal with preparing toolchain in this series
because procedure for creating it takes pretty long. So I choose two give you 
here two options:

* read my article about [Crosstool-NG arm-unknown-linux-gnueabi](/blog/2013/03/30/yet-another-quick-build-of-arm-unknown-linux-gnueabi)
* or install toolchain ready to use like [Emdebian](http://www.emdebian.org/)

#### Emdebian path ####
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
whereis arm-linux-gnueabi-gcc-4.7
arm-linux-gnueabi-gcc-4: /usr/bin/arm-linux-gnueabi-gcc-4.7 /usr/bin/X11/arm-linux-gnueabi-gcc-4.7
```
And link it to arm-linux-gnueabi-gcc:
```bash
sudo ln -s /usr/bin/arm-linux-gnueabi-gcc-4.7 /usr/bin/arm-linux-gnueabi-gcc
```

Toolchain is ready to use.

_Note_: I tried `CodeSourcery` toolchain `arm-2012.09-64-arm-none-linux-gnueabi.bin`,
but it contain `binutils` defect that not allow correctly build kernel. If you see
something like this in log:
```
Error: selected processor does not support ARM mode `ldralt lr,[r1],#4'
```
That means you experience same thing, please use `Emdebian` or `Crosstool-NG` toolchain.

### Summary ###
If you take effort of creating toolchain using Crosstool-NG than 
congratulations.  But for simplifying whole [__Virtual Development Board__](/blog/2013/04/22/intro-to-virtual-development-board-building)
series I will use `Emdebian` toolchain in further posts. Of course you can use 
your brand new `Crosstool-NG` toolchain by simply remember that tools prefixes are 
different. `Emdebian` uses `arm-linux-gnueabi-` and `Crosstool-NG` was created with 
`arm-unknown-linux-gnueabi-`. Replace one with another every time when needed.
In [next post](/blog/2013/04/22/embedded-board-bootloader) we will deal with 
bootloader.

### Kudos ###

1. [Toolchains](http://elinux.org/Toolchains)

