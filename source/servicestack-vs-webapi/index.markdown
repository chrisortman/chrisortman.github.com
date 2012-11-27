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
 * Feels more stable than WebAPI, given that WebAPI and MVC probably need to further converge I expect there to be quite a bit of churn in the code. Several prominent asp.net team members have also left in the past year.
 * After nearly a decade of building web API's on almost every Microsoft stack in existence the RequestDTO / ResponseDTO pattern feels the most correct and natural. Usually with WCF I would wind up generating service reference or referencing data contracts if that was an option and then creating request & response objects for them anyway.
 * You can handle SOAP & REST from the same codebase (not that you really want to build anything with SOAP, but sometimes it is your only choice)

####Cons
 * Metadata page is ugly
 * Can feel like a big pill to swallow at times because of emphasis placed on compatibility with Redis and Dart. I think it is good to provide a complete stack OOB, but since those are not things I care about it makes me wonder if the framework will evolve towards those things and make the story if you are not using them less compelling.
 * I'm not a big fan of bundling FluentValidation and Funq with the framework, they never get in my way and I don't notice them...they are just not libraries I would typically choose.	
 * I expect this to turn people off: "We intend to improve this story further with a commercial VS.NET extension to enable 'Add ServiceStack Reference' like behaviour providing a familiar productive development experience for existing VS.NET SOAP WebService developers." Not that I am against the ServiceStack guys developing something commercial its just that I know it will be a point of contention because a money has already been paid for Visual Studio and this is a built in feature there.
 * I could see you getting into a situation where you have to conform to an already existing API that would be more difficult with ServiceStack than with WebAPI because WebAPI already has the raw HTTP stuff right there waiting.
 * Sometimes it's hard to keep up with what is going on if you aren't watching regularily. Case in point I'm not exactly sure if ServiceStack supports async / await on their services yet or not. I can find some examples from many months ago, but nothing very recent.



##WebAPI

####Pros
 * The roadmap includes help page generation, and being able to generate a nice looking help page is something I care a lot about
 * Generally less politically devisive for a Microsoft shop since it comes from Microsoft.
 * Working directly with HttpRequestMessage and HttpResponseMessage is very close to http spec...ie it doesn't simplify access to headers like servicestack does with GetHeader<T>. I think this is a pro because you don't have to figure out what the API is doing as it maps very directly with what is in the HTTP spec

####Cons
 * The mapping of a URL to an action (routing) is difficult to understand (for me). I don't like having `GetAllComments` and `GetCommentById`. For some reason this just does not seem terribly straight forward.
 * Having Task all the way down can lead to some difficult to write test & client code. Async / await help this a little, but still seems to get overly complicated.
 * You need to have a request instance in order to create a response which complicates testing.
 * /Most/ [roadmap](http://aspnetwebstack.codeplex.com/wikipage?title=Roadmap) features I don't care much about.
 * Having to subclass ApiController really hurts when you want to unit test your controller
 * Having attributes like `HttpPost`, `HttpPut` and `AcceptVerbs` is confusing as it's hard to remember or know which you should use. /I think my advice is to just use AcceptVerbs always/


##Notes

 * I find myself really trying to reach for good things about WebAPI in order to appear unbiased and keep things fair, but after a few weeks with ServiceStack it feels so much simpler and easier that I struggle to not use it. Perhaps it is because I am still in the honeymoon phase?
 * I think the biggest difference comes down to the fact that ServiceStack converts the request into an object and WebAPI converts the request into a method call. This is a very profound change that has many positive implications
 * I wondered if I could maybe start modifying WebAPI to give me the behavior I love about ServiceStack. So I started digging into the code. I wanted to setup a sample website and step through all the WebAPI code to watch it handle a request. The [steps to make that happen](http://vibrantcode.com/2012/06/06/look-ma-no-gac/) are scary to me though. Granted this is not WebAPI's fault, it is a consequence of Razor being part of the same code base and the needs of Visual Studio.

##References

 * [http://www.tuicool.com/articles/2Ezmea](http://www.tuicool.com/articles/2Ezmea) - Helped me understand the evolution of the ServiceStack API
 * [http://geekswithblogs.net/JoshReuben/archive/2012/10/28/aspnet-webapi-rest-guidance.aspx](http://geekswithblogs.net/JoshReuben/archive/2012/10/28/aspnet-webapi-rest-guidance.aspx) - Coverts a lot of the functionality with WebAPI