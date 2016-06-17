---
layout: post
title: "Easy request logging on OSX"
comments: true
tags:
 -
---

Here's a great way to capture HTTP request/response traffic on OSX. I use this to help troubleshoot strange issues in Capybara/Selenium tests with Firefox

In your terminal where you are launching your tests from export these variables

```
export NSPR_LOG_MODULES=timestamp,nsHttp:3
export NSPR_LOG_FILE=~/Desktop/log.txt
```

Now when you run the tests your request and response traffic will be written to ~/Desktop/log.txt

[More Info](https://developer.mozilla.org/en-US/docs/Mozilla/Debugging/HTTP_logging)
