---
published: true
layout: post
title: "AJAX race condition in cucumber"
date: "2015-03-27 9:19"
comments: true
tags:
 - rails
 - mssql
 - cucumber
---

* cucumber
* selenium
* transactional javascript strategy
* concurrent ajax requests
* MS SQL Server
* config.cache_classes = true

After upgrading an app to rails 4.1.1 with this combination cucumber features started failing with this error

```
TinyTds::Error: Attempt to initiate a new Adaptive Server operation with results pending: BEGIN TRANSACTION (ActiveRecord::StatementInvalid)
```

TLDR; You can fix it by changing config.cache_classes to false

After our page loads it immediately fires off 2 xhr requests to the server.

Because these requests hit the server at close to the same time and we are using 
transactional rollback they use the same database connection. The race is that
a second query is issued before the results of the first are enumerated.

My first thought was this shouldn't happen because Capybara launches the server in 
a single thread so only one request at a time right? But that's not quite how it
works.

It is using webrick which is multithreaded so it will spawn new threads to handle 
these requests. In development mode rails forces webrick to be single threaded
by injecting a mutex into the rack middleware, but if you have config.cache_classes = true
(I think this is the default) it doesn't do this 

Changing this value to false will slow you down, but your server will go back to being
single threaded and you won't have this problem with your SQL Server connection
