---
layout: post
title: "Friday:Beer+Code"
date: 2012-10-19 22:07
comments: true
categories: 
---

On tap: [Leinenkugels Summer Shandy](https://www.facebook.com/pages/Leinenkugels-Summer-Shandy/43479912786)  
On the tube: [Fellowship of the Ring](http://www.imdb.com/title/tt0120737/)

My dad has asked me to build him a small app to help him put together car listings and sync them between cars.com, craigslist and various other sites. I thought a WPF app and a SQL CE database would fit quite nicely for this. I have also been looking for an excuse to play with some of the new [ReactiveUI](http://github.com/reactiveui/reactiveui) bits so win / win there. His computer is still on XP so it will have to target .NET 4.0

My biggest concern with this tech choice is that I expect he will delegate a lot of the actual management to his better half so I will wind up needing to have the app run on multiple computers which will beg for a shared database of some kind.

I figure that for starters EntityFramework will be the easiest thing to get started with. 

You run into some problems here because `install-package entityframework` wants to pull down EF 5, but EntityFramework.SqlCompact is still depending on EF 4.3.1. Also since I'm using XUnit for my tests thier test runner wants you to target 4.5 and using EF 4.3 on .NET 4.5 causes some warnings (though I don't think they will matter)

I don't do this enough for desktop apps, and boy for some reason I can't seem to remember that static Database class to configure my initializers. I also thought about storing the database file in a user specific folder, but that isn't a front and center option and doesn't turn up in the top 3 of a google query so it will require a little investigation.

