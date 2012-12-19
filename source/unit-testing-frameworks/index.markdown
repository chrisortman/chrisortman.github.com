---
layout: page
title: "unit testing frameworks"
date: 2012-11-28 07:07
comments: true
sharing: true
footer: true
---

##XUnit.net 

This is my favorite unit test framework. I think the decision to base setup / teardown on regular .NET semantics (ctor and IDispose) was a very elegant move. Of all the unit test frameworks I have tried (xunit, nunit, mstest, mbunit) this also has the best extensibility model.

##MSTest

MSTest has gotten passable as a choice in VS2012. It still suffers from the desire to create the TestResults directory which means a lot of unecessary file copies happen and slow you down on larger projects with many DLLs. I have also run into problems with their ClassInitialize and ClassCleanup functions (which have to be declared static sadly) in that I could not get them to work when trying to setup an in-memory HttpListener to do webservice functional/integration testing.