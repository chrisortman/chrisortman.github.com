---
layout: post
title: "IE7 and string indexers"
date: 2013-02-06 09:42
comments: true
categories: 
---

One thing that has always been painful when writing javascript that is going to call back to an ASP.Net site is virtual directories.

You can't rely on / getting you to the top of your path if you are running from a virtual directory (named Vdir) you need /Vdir

This gets a little more problematic when I don't know what the virtual directory will be called. 

On the server there is an elegant solution, use ~/ for your paths and the server will expand it for you. I wanted the same behavior for my jQuery.ajax calls, so wrote this little helper which I put into my razor layout file:

{% highlight javascript %}
$.ajaxPrefilter(function (options) {
	var applicationPath = '@Context.Request.ApplicationPath';
    if (!endsWith(applicationPath,"/")) {
    	applicationPath += "/";
    }
    
    if (options.url[0] == "~") {
    	options.url = options.url.replace("~/", applicationPath);
    }
    
});
{% endhighlight %}

This always worked fine when testing locally, but after deploying it out to staging servers some users ran into problems.

To reproduce it I setup a local IIS website. If I ran the site over a localhost url everything worked fine, but as soon as I used my machine name it would break and all ajax requests would get sent to /vdir/~/api/... instead of /vdir/api/... 

As I stepped through the code I noticed that `options.url[0]` returned `undefined` instead of a `~`

After some searching I wound up on [this stack overflow question](http://stackoverflow.com/questions/5943726/string-charatx-or-stringx)

So IE7 doesn't like the string indexer, but I'm on IE10...or so I thought.

I checked the IE developer tools saw I was running in IE10 Compat View and IE7 Standards Doc mode. 

So why is my site being forced into IE7 mode?

Microsoft has a helpful doc [here](http://msdn.microsoft.com/en-us/hh779632.aspx)


Step 3: Determine why your site is not in Standards Mode

Most problems are related to supporting older versions of IE.  Start by ensuring your standards-based code is rendered in IE9 and 10.  Then keep your non-standards-based code for older versions of IE.

1. My page is not in Browser Mode: IE10

	Possible Cause: Your website may flagged in Compatibility View and forced into an older browser mode to ensure the site functions
	Resolution: Check if your site is on the list here.  Learn more about the Compatibility View list and request removal here.

2. My page is not in Document Mode = IE10

	Possible Cause: Your website’s doctype is invalid or missing
	Resolution: Check for a valid, well-formed doctypelike:


----
1. I am running off my local machine, so my website is not flagged.
2. I have `<!DOCTYPE html>` 

So what else. In IE if you goto Tools -> Compatibility Mode Settings there is an option to render all intranet sites in compatibility mode. That would make sense why it broke when I switched from http://localhost to http://machinename

My first fix then is to disable that setting. But I will have other users that will run this site via a local intranet so I need to make sure they run in IE10 standards mode and that my javascript works in their browser.

I can set this meta tag to give IE a hint

{% highlight html %}
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
{% endhighlight %}

And also switch my function to use `charAt` instead of `[]`

----

Another strange IE problem I ran into today. If you have a call to `console.log` in your code and you are running in IE9 or less you will get an error, but if you open the developer tools to try to debug the error then `console.log` will succeed and you can't troubleshoot the error. This behavior has been known to induce violent swearing in some programmers.