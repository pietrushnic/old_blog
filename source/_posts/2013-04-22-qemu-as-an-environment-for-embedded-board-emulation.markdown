---
layout: post
title: "0x1: Virtual Development Board"
date: 2013-04-22 08:27
comments: true
categories: [embedded, linux, vdb]
published: false
keywords:
description:
---
## Table of contents ##

1. [Intro to Virtual Development Board building](/blog/2013/04/22/intro-to-virtual-development-board-building)
2. [-> Qemu as an environment for embedded board emulation <-](/blog/2013/04/22/qemu-as-an-environment-for-embedded-board-emulation)
3. [Toolchain for Virtual Development Board](/blog/2013/04/22/toolchain-for-virtual-development-board)
4. [Embedded board bootloader](/blog/2013/04/22/embedded-board-bootloader)
5. [Linux kernel for embedded system](/blog/2013/04/22/linux-kernel-for-embedded-system)
6. [Qemu network configuration and tftp for Virtual Development Board](/blog/2013/04/22/qemu-network-configuration-and-tftp-for-virtual-development-board)
7. [Root file system for embedded system](/blog/04/22/root-file-system-for-embedded-system)
8. [Root file system over NFS]()
9. [Final booting and ideas for improvements]()

## Qemu as an environment for embedded board emulation ##

QEMU is a CPU emulator using dynamic binary translation to convert guest CPU 
instructions into host CPU instructions. It supports many
architectures from x86, through ARM and MIPS, to MicroBlaze. According to 
compilation configuration target list QEMU targets 26 different softmmu types. Only for ARM it 
supports 33 machines (like ARM Versatile/PB (ARM926EJ-S) or Samsung NURI board 
(Exynos4210)) and 28 CPUs (with cortex-a9 and pxa270). It gives access to 
network, storage, video, usb, serial and other peripheral, also user defined. It
is developed under GNU GPL, so everybody are free to make modifications, improve
and extend it. This properties makes QEMU very good candidate for virtual board emulator.

### Compilation ###
Let's start creating our Virtual Development Board. As usually I will use latest
greatest version from git:
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
offer to initialize it as a submodule.
```
git submodule update --init pixman
make # restart compilation process
make install
```
If compilation ends without problem than our first component is ready to use.
Right now we can emulate our ARM based board with many types of CPUs. List of 
all available can be retrieved by running command `qemu-system-arm -cpu ?`, list 
of emulated machines by `-M ?`. Now, let's talk about [toolchains](/blog/2013/04/22/toolchain-for-virtual-development-board).

### Kudos ###
1. [Dynamically Translating x86 to LLVM using QEMU](http://infoscience.epfl.ch/record/149975/files/x86-llvm-translator-chipounov_2.pdf)
