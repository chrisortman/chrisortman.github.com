---
layout: post
title: "Friday: beer+code"
date: 2012-10-05 22:32
comments: true
categories: 
---

On tap: [Leinenkugels Summer Shandy](https://www.facebook.com/pages/Leinenkugels-Summer-Shandy/43479912786)  
On the tube: [Avengers](http://www.imdb.com/title/tt0848228/)

Today I am setting up an [octopress](http://octopress.org) blog. After [what happened with posterous](http://blog.posterous.com/big-news) I really like the idea of owning my own content/data. I have tried several methods of journaling and blogging before, but struggle to stick with it. I think the git and simple text editor nature of octopress will work for me because it fits into my natural workflow.

I have been very enamored with the ideas of clojure and lisp lately. Mostly the ability to build your own language within them. I also love the purity of how everything is a list. I am working my way through Clojure Programming (currently at location 3483)

Am using [MarkPad](http://code52.org/DownmarkerWPF/) to write this, but it keeps complaining about not being able to monitor locked files under .git, I suppose we can fix that...

I'm going to dig into the MarkPad source code, and realize I should post a screenshot of the error I'm getting so I quick [check on how to do that with octopress](https://www.google.com/search?oq=octopress&sugexp=chrome,mod=7&sourceid=chrome&ie=UTF-8&q=octopress#hl=en&sclient=psy-ab&q=octopress+images&oq=octopress+images&gs_l=serp.3..0l2j0i8j0i22.5591.6913.0.7065.7.5.0.2.2.0.180.769.0j5.5.0.les%3B..0.0...1c.1.WX0tFXap4GM&pbx=1&bav=on.2,or.r_gc.r_pw.r_cp.r_qf.&fp=304ddafb65aaa7e0&biw=903&bih=913). It looks pretty easy. 

{% img screen_shots/markpad_crash2.png %}

I know it must be related to monitoring of a file or directory, so I do a git grep "FileSystemWatcher" which leads me to [FileSystemWatcherWrapper](https://github.com/Code52/DownmarkerWPF/blob/master/src/MarkPad/Infrastructure/Abstractions/FileSystemWatcherWrapper.cs)

Simple fix, [pull request]() submitted.

