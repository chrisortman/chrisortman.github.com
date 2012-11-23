---
layout: page
title: "servicestack vs webapi"
date: 2012-11-23 15:11
comments: true
sharing: true
footer: true
---

##Service Stack

####Pros
 * Very easy to get up and going quickly. Easy to explain to others what is going on
 * Really like what you can have for a C# client because of the IReturn<T> interface
 * Functional testing using the AppHostListener was very easy to use.
 
####Cons
 * Metadata page is ugly


##WebAPI

####Pros


####Cons
 * The mapping of a URL to an action (routing) is difficult to understand (for me). I don't like having `GetAllComments` and `GetCommentById`. For some reason this just does not seem terribly straight forward.
 * Having Task all the way down can lead to some difficult to write test & client code. Async / await help this a little, but still seems to get overly complicated.
 * You need to have a request instance in order to create a response which complicates testing.