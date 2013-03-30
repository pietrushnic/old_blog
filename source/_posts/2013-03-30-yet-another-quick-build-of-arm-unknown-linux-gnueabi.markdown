---
layout: post
title: "Yet another quick build of arm-unknown-linux-gnueabi"
date: 2013-03-30 05:10
comments: true
categories: 
published: false
keywords:
description:
---

So I decide check what is going on with crosstool-ng and refresh my old post 
about building arm-unknown-linux-gnueabi toolchain. Last post was pretty 
popular, so definitely this is direction I should follow :). I will not repeat 
myself, so if you encounter any problems please check 
[this](/blog/2012/03/14/quick-build-of-arm-unknown-linux) and of course 
`more /path/to/ct-ng/src/crosstool-ng/docs/B\ -\ Known\ issues.txt`. Let's 
begin:

### Get the latest crosstool-ng ###

As usual I'm trying to use latest version possible. Following the page:
```
hg clone http://crosstool-ng.org/hg/crosstool-ng
cd crosstool-ng
./bootstrap
```
At the time of writing this article my changeset was 3200:0fc56e62cecf 16 Mar 
2013, two weeks old.

### Installation ###

I prefer to use local directory for ct-ng in case it will change in feature I 
will not need to mess with `/usr` subsystem. Tryin' to keep it clean when I can.

```
mkdir $HOME/ct-ng
./configure --prefix=$HOME/ct-ng
make
make install
```
No problems on my up to date Debian wheezy.
You will probably want to add `$HOME/ct-ng` to your `PATH`
```
export PATH="$HOME/ct-ng/bin:${PATH}"
```
An bash completion as it is advised at the end of compilation process. My `.bashrc`
automatically sources `$HOME/.bash_completion` so ther is a place for local 
completion code.
```
cat ct-ng.comp >> $HOME/.bash_completion
```

### Build sample toolchain ###

There is a long list of predefined sample toolchain which you can get. 
I completion was correctly added, than you can explore it by `<Tab>` or simply 
`ct-ng list-samples`. Let's try to build `arm-unknown-linux-gnueabi`:
```
mkdir -p $HOME/embedded/arm-unknown-linux-gnueabi
cd $HOME/embedded/arm-unknown-linux-gnueabi
ct-ng arm-linux-unknown-gnueabi
```

Take a break/write blog post/play game anything - this will take a while.


