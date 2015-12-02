---
layout: post
title: "Easy, Medium, Hard"
comments: true
tags:
 -
---

I have found it useful to break technical problems into Easy, Medium, and
Hard defined like this

 * Easy - We know which code will need to change and the code to change is
   very isolated and does not impact other parts of the system or we are only 
   adding new code
 * Medium - We have a pretty good idea what to do, but we're not certain
   of the ramifications or know that we are changing some existing
	 functionality.
 * Hard - We have some theories about how it might work, but lots of
   uncertainty. We will need to either build a prototype, or spike a solution
	 to learn more. At this point just consider whatever you're talking about
	 as R&D

This works when you're trying to decide whether to commit to something or
just trying to get an idea of how risky a project might be.

If you have a bunch of easy things you can be reasonably confident about
estimates and likely depending on your code speed delivery with more people.

With medium things I expect the development work to be reasonably easy to
define, but know that extra time will need to be given to regression and
system testing. Sometimes more work will come out of that, or you might 
encounter something that causes you to need to backpedal on the feature.

Hard is hard. You need to do more work to turn hard into a medium or easy.

