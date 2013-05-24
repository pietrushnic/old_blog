---
layout: post
title: "Workflow imporovement with Open Source tools"
date: 2013-05-16 22:16
comments: true
categories: 
published: false
keywords:
description:
---

I want to start a series of articles to describe how I improve my workflow with 
geeky Open Source applications. I will concentrate on terminal applications that 
I try to use in my GTD process.

### Terminal ###
#### Solarized gnome-terminal ####
If you already don't know what solarized color scheme ii then please take a look 
at [this page](http://ethanschoonover.com/solarized). To install solarized 
colorscheme in gnome-terminal simply follow instruction from this [git repository](https://github.com/sigurdga/gnome-terminal-colors-solarized).

### Mutt again ###
During last days I spent a lot of time to tweak my mutt configuration. Here I 
will give you few hints about things that I learned.

#### Solarized mutt ####
To enable solarized colorscheme in mutt simply download one of scheme files from 
[github](https://github.com/altercation/mutt-colors-solarized). If you installed 
dark scheme for your gnome-terminal then I suggest `mutt-colors-solarized-dark-16.muttrc`.
Copy this file for example to `$HOME/.mutt` and source it in muttrc file:
```
source $HOME/.mutt/mutt-colors-solarized-dark-16.muttrc
```

#### Width of From column ####
Usually from field in today's emails is longer than 19 characters. If this 
happens mutt by default will not display whole string. To change this behaviour 
you can manipulate `index_format` variable. I set mine to 30:
{% raw %}
```
set index_format="%4C %Z %{%b %d} %-30.30L (%4l) %s"
```
{% endraw %}

#### Mutt sidebar ####
Instead of using change folder feature you can install 
[mutt-sidebar](http://www.lunar-linux.org/mutt-sidebar/). It will give you great 
overview of your mailboxes, improve mutt look and feel and hopefully 
productivity. As I wrote in [previous post](/blog/2013/05/15/thunderbird-to-mutt-migration/) I'm in favor of compiling mutt from sources. To apply patch follow instruction from website.
Unfortunately instruction are available for latest release version 1.5.21.
```
hg clone http://dev.mutt.org/hg/mutt#HEAD
hg update -C HEAD
hg pull -u
hg checkout mutt-1-5-21-rel
./prepare --with-ssl --enable-debug --enable-imap --enable-smtp --enable-pop \
--enable-hcache --with-gss --with-gnutls --with-sasl
wget -q -O - http://lunar-linux.org/~tchan/mutt/patch-1.5.21.sidebar.20130219.txt | patch -p1
make && sudo make install
```
