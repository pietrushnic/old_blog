---
layout: post
title: "Root file system for embedded system"
date: 2013-04-22 10:40
comments: true
categories: [embedded, linux, vdb]
published: false
keywords:
description:
---

To make our embedded linux work as virtual development platform we need some 
environment after booting. There is many approaches to get working root file 
system but I will use the easiest one as a exercise. I don't want to create full 
embedded distribution (this is good plan for future works). Right now I will be 
happy with simple initramfs.
