---
layout: post
title: "When Gemfile requires oldest version of rake"
date: 2013-03-19 16:00
comments: true
categories: [octopress, rails]
published: false
keywords: rails,rake
description: Gemfile requires oldest version of rake
---

It's been a while since I installed rails on my Debian. In mean time I performed 
clean up, which as usual ends with damaged system, after restoring I encounter 
below rake error message:
```bash
pietrushnic@lothlann:~/src/pietrushnics_world$ rake preview
rake aborted!
You have already activated rake 10.0.3, but your Gemfile requires rake 0.9.6. Using bundle exec may solve this.
/var/lib/gems/1.9.1/gems/bundler-1.3.4/lib/bundler/runtime.rb:33:in `block in setup'
/var/lib/gems/1.9.1/gems/bundler-1.3.4/lib/bundler/runtime.rb:19:in `setup'
/var/lib/gems/1.9.1/gems/bundler-1.3.4/lib/bundler.rb:120:in `setup'
/var/lib/gems/1.9.1/gems/bundler-1.3.4/lib/bundler/setup.rb:7:in `<top (required)>'
/home/pietrushnic/src/pietrushnics_world/Rakefile:2:in `<top (required)>'
(See full trace by running task with --trace)
```
