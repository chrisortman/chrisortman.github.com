---
layout: post
title: "ZSH bind a key to reload your RVM config"
comments: true
tags:
 - shell
 - zsh
---

I'm working on a project that is in the process of upgrading from rails 2 to rails 3.
Because I'm switching branches I have to frequently reload my RVM configuration to
switch between 1.8.7 and 1.9.3 so I wanted an easier way to do it.

I wrote a function that will just cd out and back into my directory causing the
normal hooking / loading mechanisms.

```bash

function reload-dir () {
  cd ../
  cd $OLDPWD
  zle .accept-line
}
```

zle .accept-line is a zsh built-in that is the equivalent of pressing enter.

This was a new concept for me, but I can really see some power there. You can
read more about it in section 4.7.3 [http://zsh.sourceforge.net/Guide/zshguide04.html#l76](here)

Having the function is nice, but I want to bind it to a key `Ctrl+R`

```bash
zle -N reload-dir
```

This again uses that zle built-in to register our function so that we can bind it with

```bash
bindkey '^r' reload-dir
```

