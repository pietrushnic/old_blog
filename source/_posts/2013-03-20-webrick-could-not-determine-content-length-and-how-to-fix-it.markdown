---
layout: post
title: "WEBrick: \"Could not determine content-length\" and how to fix it"
date: 2013-03-20 22:39
comments: true
categories: octopress
published: false
keywords: webrick,octpress,ruby
description: How to get rid of WEBrick warning about content-length
---

While runing `bundle exec rake preview` I encounter this annoying warning:
```
[2013-03-20 22:36:14] WARN  Could not determine content-length of response body. Set content-length of the response or set Response#chunked =
```

My ruby version is `ruby 1.9.3 (2012-04-20) [x86_64-linux]`. Discussion 
[here](https://github.com/imathis/octopress/pull/803) doesn't prove that 
[stackoverflow best solution](http://stackoverflow.com/a/10249177/587395) for replacing WEBrick with
Thin. Thats why the best way to fix that right now is patching httpresponse.rb 
by myself :). I use rvm so `find ~ -iname httpresponse.rb` gives me file path.
```
vim /.rvm/rubies/ruby-1.9.3-p392/lib/ruby/1.9.1/webrick/httpresponse.rb
```
Find `Keep-Alive connection` and fix line:
```ruby
if chunked? || @header['content-length']
```
with:
```ruby
if chunked? || @header['content-length'] || @status == 304 || @status == 204
```

That's all. If you have any questions just comment. I feel this post useful 
please share.

