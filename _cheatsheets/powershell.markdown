---
layout: post
title: Powershell
---

Allow running of scripts (must be admin)

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
```

Check that a file exists

```powershell
if(Test-Pah file.txt) { #your code here }
if(!(Test-Path file.txt)) { #your code here }
```

Get the current directory of your script

```powershell
$mydir = (Split-Path -Parent $MyInvocation.MyCommand.Definition)
```

Combine your current directory and build a new path

```
$mydir | Join-Path -ChildDir "subdir" | Join-Path -ChildDir somefile.txt
```

