---
layout: post
title: "why you should learn docker"
comments: true
tags:
 - talks
 - docker
---

#Docker

[slides](https://speakerdeck.com/chriso/why-you-should-learn-docker)

Docker has been a hot commodity for the past couple years. A lot is happening around it and even the big companies are getting involved 

* Docker and Microsoft Partner to Drive Adoption of Distributed Applications
* Dell Simplifies the Cloud Experience with Docker
* AWS Announces the EC2 Container Service for managing Docker containers
* IBM and Docker Announce Strategic Partnership to Deliver Enterprise Applications
* Docker + VMware = better together

[https://www.docker.com/company/news/]()

My hope is that this presentation will spawn a regular meeting of
people that are interested in this technology. But, in order to do that
I need to convince you this is worth exploring further.

Stand by for sales pitch

###Programmers

Programmers, what if your first day on a new job or a new team I told
you that in order to work on the application code you have to first
download these zip files from the internet and extract them to a 
specific location on your computer. Then change variables in these
certain files in order to match your machine. Next, ftp files from
this server into this specific location and don't worry if it 
overwrites some of the other files you just downloaded. Finally 
copy and paste this text from this email into a file called 
Makefile and then you should be able to build the code.

Now if you have to make changes to anything (which I hope you will
if you plan to fix any bugs or add any features) Make a backup copy
  of the file you're changing. You might notice some other backups there
  so just use old1..N or bak or something like that. Then take the copies 
  of your files back to where you originally got them from and either send an
  email to the group with what you changed.

Would anyone not run screaming from this job? It would be crazy right?

But this is what we do with our servers isn't it?

* download & ftp => yum 
* variables => httpd.conf, freetds.conf
* copy & paste shell commands

This isn't just for our application servers either is it. How about a wiki page for your build server? I think everytime I have had to make one of these it all goes fine for the first few commands and then things start getting sketchy. Some version is wrong or something changed in the distro.

###Sysadmins

Sysadmins, do I have anyone here that would identify themself as more of
as sysadmin than a programmer?

So what if I told you that from now on the only thing you'll have to run
on your server will be a .exe file. Every application, service, daemon
will come to you as a single self contained executable file and any 
upgrades will be as simple as replacing that file.

No conflicts, no instructions, just copy the file and run.

I would assume that many of you manage virtual servers? Would it help
if you didn't have to deal with resource allocation anymore? No more 
  arbitrarily deciding how much ram to put into an instance only to 
  have to change it later when one application grows faster than the 
  other. You can leave all this management to the kernel.

 
###Demo

Now this is a short talk so we might not have time to get through everything.
Valera said his team is using Jira for issue tracking and TeamCity
for build automation.

Let's see if we can get one of those setup and configured in the next
few minutes. Which should we do Jira or TeamCity?

Jira? Ok.

```bash
docker run --publish 8080:8080 cptactionhank/atlassian-jira
```

And then we can view the site in firefox

```bash
open -a firefox -g http://$(boot2docker ip):8080
```

I guess maybe we have time for TeamCity too

```bash
docker run --publish 8111:8111 sjoerdmulder/teamcity
open -a firefox -g http://$(boot2docker ip):8111
```

What do you think? Does this not just blow your mind? Are you not
entertained!

Let's explore a little and see how this is possible

```
➜  docker run -t -i ubuntu /bin/bash
root@48023c4a65e9:/# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@48023c4a65e9:/#
```

What happened, I just started a virtual operating sytem
as defined by my ubuntu template, and loaded a bash shell

> I can't even find the VMWare icon on my computer that fast.

There's no apache, no drupal, jira or anything. This is a clean slate.

Working in here *feels* like working in a virtual machine. And it kind of is.
But it's different because the virtual machines that you're familiar with
are virtualized at the *hardware* level and Docker virtualizes at the
*operating system* level.

But I can treat this like a virtual machine. And make configuration changes

```
root@291524fcfe63:/# which curl
root@291524fcfe63:/# apt-get -y install curl
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following extra packages will be installed:
  krb5-locales libasn1-8-heimdal libcurl3 libgssapi-krb5-2 libgssapi3-heimdal
  libhcrypto4-heimdal libheimbase1-heimdal libheimntlm0-heimdal
  libhx509-5-heimdal libk5crypto3 libkeyutils1 libkrb5-26-heimdal libkrb5-3
  libkrb5support0 libldap-2.4-2 libroken18-heimdal librtmp0 libsasl2-2
  libsasl2-modules libsasl2-modules-db libwind0-heimdal

#####    MORE APT-GET STUFF   #####

Setting up libsasl2-modules:amd64 (2.1.25.dfsg1-17build1) ...
Setting up curl (7.35.0-1ubuntu2) ...
Processing triggers for libc-bin (2.19-0ubuntu6.6) ...
root@291524fcfe63:/# which curl
/usr/bin/curl

root@291524fcfe63:/# curl uiowa.edu
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="http://www.uiowa.edu/">here</a>.</p>
<hr>
<address>Apache/2.2.15 (Red Hat) Server at uiowa.edu Port 80</address>
</body></html>
root@291524fcfe63:/#
```

So I installed something and it works.

Everytime I use this run command I get a new virtual operating system
built from my template. Which means if I were to rerun the same command
I would not have curl installed anymore.

If I want curl to be available in my container I have to put it
into my image.

I'm going to use another terminal to query my docker host and ask it
to show me all the containers it has running

```bash
➜  docker ps
CONTAINER ID        IMAGE                                 COMMAND                CREATED             STATUS              PORTS                    NAMES
46a604f7e5bf        ubuntu:latest                         "/bin/bash"            7 minutes ago       Up 6 minutes                                 mad_davinci
ffdb0ceb120b        drupal:latest                         "apache2-foreground"   12 minutes ago      Up 12 minutes       0.0.0.0:8112->80/tcp     hopeful_meitner
2709e2a146e2        cptactionhank/atlassian-jira:latest   "/usr/local/atlassia   18 minutes ago      Up 18 minutes       0.0.0.0:8080->8080/tcp   suspicious_albattani
76402f710877        sjoerdmulder/teamcity:latest          "/sbin/my_init"        19 minutes ago      Up 18 minute
```
I see my /bin/bash container here named `mad_davinci`. I can refer to it by that name or its CONTAINER ID which you also see printed in 
the shell window.

What I want to do is tell docker to create a new image based on the way my container is now.

```bash
➜  docker commit -m 'Installed curl' -a 'Chris Ortman' mad_davinci chrisortman/curl-ready
019e5428e730c623ea2486d41bac5c3777536a6d66f9ee14da7630d71c3c7ad2
```

I've tagged my new image so that now I can use the name to run new containers

I can see my image now when I ask docker to show me images

```bash
➜  docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
cptactionhank/atlassian-jira   latest              cf0627981787        27 hours ago        893.1 MB
chrisortman/curl-ready         latest              019e5428e730        4 days ago          199.7 MB
microsoft/aspnet               latest              c3cad6068e48        8 days ago          729.1 MB
drupal                         latest              61901de764c5        12 days ago         471.2 MB
ubuntu                         latest              07f8e8c5e660        2 weeks ago         188.3 MB
sjoerdmulder/teamcity          latest              06ba72713bb1        3 weeks ago         1.27 GB
```

And run it 
```bash
➜  docker run -t -i chrisortman/curl-ready /bin/bash
root@0c265dd08153:/# which curl
/usr/bin/curl
root@0c265dd08153:/#
```

This is one way to build images, another way would be to use Dockerfiles which capture all your instructions in a file that can be executed by docker.

What docker looks like then is this

![Docker flow](/assets/images/docker-flow.png)

<div class="sr-only">
Container --created from--> Image --downloaded from--> Registry

Dockerfile --build instructions--> Image --stored in--> Registry
</div>

1. Registry - Storage location for images. An example is DockerHub which is like Github but for servers. You can host your own also
Containers are built from templates called *images*. I can also save the container state to be used as a template for another container
2. Container - This is your virtualized operating system. Uses namespaces, control groups, and union file systems.
3. Image - Readonly templates that contain your operating system and files. Images are stored in *registries*
4. Dockerfile - A Makefile for your image. Contains the steps needed to produce the container.
So what does *run* mean.

I hope this has given you an idea of the capabilities and potential of Docker.

I really think this is the future of application delivery. 

If you're interested in being part of a docker group please contact me.

Questions??

###Resources

* [docker.com]()
* [https://www.joyent.com/developers/videos/docker-and-the-future-of-containers-in-production]()
* [https://www.youtube.com/watch?v=GVVtR_hrdKI&index=9&list=PLwyG5wA5gIzjhW36BxGBoQwUZHnPDFux3]()

