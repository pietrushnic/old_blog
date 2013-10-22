---
layout: post
title: "0x0: Intro to Virtual Development Board building"
date: 2013-04-22 01:27
comments: true
categories: [embedded, linux, vdb]
published: false
keywords: virtual development board, qemu, linux, embedded
description: Intrudction to virtual embedded development board building
---
## Table of contents ##

* [Preface](/blog/2013/04/22/intro-to-virtual-development-board-building/#preface)
* [First question: How to start ?](/blog/2013/04/22/intro-to-virtual-development-board-building/#first-question)
* [Virtual Development Board](/blog/2013/04/22/intro-to-virtual-development-board-building/#virtual-development-board)
* [Kudos](/blog/2013/04/22/intro-to-virtual-development-board-building/#kudos)

<a id="preface"></a>
### Preface ###
There are many reasons why ambitious programmers should care about huge rush in 
embedded market. IMO there is great potential in it. I don't want to go deep into
this but take a look at all tablets, phones, smart TVs and other cool stuff 
like wireless in you washing machine or refrigerator (ok this two are right now only a gadgets :) ).
This is all embedded. Another thing is that wages of experienced embedded
developers are really high. 

This environment is very interesting. A lot to learn, a lot to do, so why not get 
your hands dirty ?

<a id="first-question"></a>
### First question: How to start ? ###
To do real embedded development, to learn the most, you simply buy hardware like
all this `${animal}board (animal={panda,beagle,hawk})`. This is the best way but 
if you're beginner (like me) you probably don't want to spend $50 for stuff that 
you throw away after month because you don't like to deal with it or problems 
are not on your level, than maybe you should take a look around and wonder if it
is possible to build virtual environment to check what kind of problem you may
encounter learning embedded ? BTW this is cheap and fun, so potential ROI is big 
enough to try.

IMHO this is where world going: proof of concept embedded platforms affordable
for common man (not only for US/West Europe), testing environment based on
virtualized/emulated solution, to check basic features, speed up testing, development
and deployment. That's why we see so active development of software like Qemu/KVM.

<a id="virtual-development-board"></a>
### Virtual Development Board ###
I realize that it is achievable and I will learn a lot trying to implement this 
idea. Finally I was convinced reading [this](http://www.elinux.org/Virtual_Development_Board)
two years old article on [eLinux.org](http://www.elinux.org).

I decide to divide this article on few parts because IMHO it was to big to read
it at once. Also people interesting in particular topic won't have to scrolling `>2k`
words post.

<a id="kudos"></a>
### Kudos ###

At the and of this intro I want to give big kudos to all those hackers without 
whom creating this article would not be possible. Thanks for sharing your 
knowledge. Every time when I use knowledge that found by googling I'm trying
to stick link for further reading.

####[_Ejoy!_](/blog/2013/04/22/qemu-as-an-environment-for-embedded-board-emulation)####
