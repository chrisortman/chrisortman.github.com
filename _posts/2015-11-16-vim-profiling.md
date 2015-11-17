---
layout: post
title: "VIM profiling"
comments: true
tags:
 -
---

VIM was very laggy for me after when I dialed my key repeat up. I know you're not supposed to
but I sometimes just want to hold down j and scroll

When I would do this it would keep scrolling another 3-5 seconds after I let off the key.

Did some research and leared two very useful profileing tools built into VIM

```
:profile start profile.log
:profile func *
:profile file *
" At this point do slow actions
:profile pause
:noautocmd qall!
```

This will let you track down plugins that are causing performance problems

And sometimes your problem is syntax highlighting (mine was)

```
:syntime on

:syntime report
```

[http://stackoverflow.com/questions/12213597/how-to-see-which-plugins-are-making-vim-slow]()

[http://stackoverflow.com/questions/19030290/syntax-highlighting-causes-terrible-lag-in-vim]()

