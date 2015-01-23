---
layout: post
title: "Using set-upstream-to"
tags: git
 -
---

One way to avoid having to specify which remote & branch you want to use when issuing pull & push commands in git is
git checkout <story_branch>
git branch --set-upstream-to=origin/<story_branch>

Now it will always push & pull from that ref

You can view these by looking in git's config file:
vi .git/config

You will see a [branch <story_branch>] section where it will show you your 
configured remote and which branch it merges with. The way to read that section of config is:

>> for the *branch* <story_branch> in the remote <what is on the right hand side of = on the remote line> 
>> merge it into <what is on the right hand side of = on the merge line> when no merge refspec is given 
