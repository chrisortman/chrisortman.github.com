---
layout: page
title: "powershell snippets"
date: 2012-10-15 13:34
comments: true
sharing: true
footer: true
---

Approve all your .received files in a directory (see also: [ApprovalTests](http://approvaltests.sourceforge.net/))

```
 gci -filter *.received.txt | %{move-item -Path $_ -Destination $_.FullName.Replace(".received.",".approved.") -force }
 ```
 