---
layout: post
title: "Friday: beer+code"
date: 2012-10-12 22:37
comments: true
categories: 
---

On tap: [Leinenkugels Summer Shandy](https://www.facebook.com/pages/Leinenkugels-Summer-Shandy/43479912786)  
On the tube: [Star Trek](http://www.imdb.com/title/tt0796366/) and [X-Men: First Class](http://www.imdb.com/title/tt1270798/)

Time for some [clojure](http://clojure.org) again. I am getting impatient to finish this book because I want to get cracking on a few projects
and working on [clojure-clr](http://github.com/clojure/clojure-clr)

Grasping some fundamentals. Let's talk about reduce.

`(doc reduce)` says:

{% blockquote %}
([f coll] [f val coll])
  f should be a function of 2 arguments. If val is not supplied,
  returns the result of applying f to the first 2 items in coll, then
  applying f to that result and the 3rd item, etc. If coll contains no
  items, f must accept no arguments as well, and reduce returns the
  result of calling f with no arguments.  If coll has only 1 item, it
  is returned and f is not called.  If val is supplied, returns the
  result of applying f to val and the first item in coll, then
  applying f to that result and the 2nd item, etc. If coll contains no
  items, returns val and f is not called.
{% endblockquote %}

So... `(reduce + [1 2])` will give us 3 and `(reduce + 5 [1 2])` will give us 8 because we apply the plus to 5, then to 1 which gives us 6 then to 2 which gives us 8

We can use `fn` to define an anonymous function, so...

```
(reduce (fn [x y] (+ x y)) [1 2])
```
will be the same as above and give us 3.

I think the thing that worries me most about clojure is that it is awesome, but it requires such a tremendous amout of brain power/study before you can understand the concepts that it will never catch on because most will deem it as not worth it.

