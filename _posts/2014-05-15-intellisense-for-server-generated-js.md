---
published: false
---

## How to get intellisense for server generated javascript in razor views

The Problem:

When you do javascript development inside visual studio you get _helpful_ intellisense and squiglies. This is great right up until the point where you need to get that javascript from the server. Maybe it is an ajax call or you might have a helper that is going to generate the JS for you.

Razor can get finicky when you are mixing it into JS code so I have a helper that looks like

	Ajax.JsonData(string varName, object blob)
    
That will take care of doing json serialization and generate a js object for me. I do this so that I don't have to mess with something like

	var thing = @Ajax.JsonData(blob); /* is this a Razor or javascript semicolon? */
    
But then anytime i use that variable in the javascript Visual Studio will think it is an error.

To work around this I create a thing_sample.js file right next to the view I'm working on and then use my helper like this:

	/// <reference path="thing_sample.js" />
    @Ajax.JsonData("thing",Model.Things)
    
and inside my sample file 

	var thing = [ ... some sample data ... ];
    
Now visual studio will think my variable exists and be able to give me intellisense on the json.
