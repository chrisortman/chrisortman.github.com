---
layout: post
title: "Docker is awesome"
comments: true
tags:
 - talks
 - docker
---

# Docker is awesome.


1. Install and run any application with a single command
2. GIT for servers
3. Executable setup documentation
4. Isolate development environments and tools

<iframe src="https://www.slideshare.net/slideshow/embed_code/key/pyvEzq0Hd7HJK7" width="476" height="400" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

## I can install and run any application with a single command

* Subversion (hosted behind apache)
* CC.Net (IIS)
* Trac (Python, IIS)

At one point, this would have been a pretty state-of-the-art setup.

Subversion was the king of source control. Branching was great as long as you never merged

CC.Net was a .NET port of the popular java project called cruisecontrol.
As far as I can remember this was the first continuous integration
server.


Trac was lightweight developer focused issue tracking. The key
differentiator was developer focused. Most issue tracking systems
at the time were manager / QA focused and were _very_ cumbersome
to use.

This was a great setup. Really.

But getting it together was awful. **Like repressed memory awful.**

I had to run both apache and IIS so there was configuration for that.

Subversion needed to support windows auth so that required some
special libraries and I think PERL

And because of the time period and java heritage you bet your ass
CC.NET had a giant xml configuration file for you to maintain.

All told I think it was a week of trial and error to finally have
everything usable.

Anything like that, what's your approach to wanting to change anything later?
*No!* don't touch it right.

This was awful.

If I had docker then I would have been done before morning break.

----
### Key Component: Registry

**Registry** is storage location where you can pull
and push preconfigured images that are ready to go.
An example is DockerHub, but you can also host your
own pretty easily.

I like to say DockerHub is like Github but for servers.

----

### [Demo 1](https://github.com/chrisortman/docker-demos/blob/20150601-crineta/demo1.sh) - setup jira, teamcity, drupal

* point out how to install boot2docker / docker
  * [download](http://boot2docker.io)
  * [chocolatey](https://chocolatey.org/packages/boot2docker)
  * [homebrew](http://brewformulas.org/Boot2docker)
* explain demo setup (vim,tmux,slime)
  * for purposes of this demo so you don't have to watch me type and mistype so many commands I'm using[vim](http://vim.org), [tmux](http://tmux.sourceforge.net), and [vim-slime](https://github.com/jpalardy/vim-slime)
  * This let's me send whatever text I highlight in my window to a terminal
  * `echo "Hello World"`
* start the docker host
  * `boot2docker up`
* Locate images using docker hub
  * `docker search jira`
* explain basics of run command
  * When I say run I'm telling docker to go get the ubuntu image and
    create and start a new container from it. Then map port 8080
    of my host to 8080 of my container
* launch jira instance
  * `docker run -d --publish 8080:8080 cptactionhank/atlassian-jira`
* launch teamcity instance
  * `docker run -d --publish 8111:8111 sjoerdmulder/teamcity`
* launch drupal instance
  * `docker run -d -p 8112:80 drupal`
* open them all in the browser
  * `open -a firefox http://$(boot2docker ip):8080 http://$(boot2docker ip):8111 http://$(boot2docker ip):8112`

Ok, this is pretty amazing right?



## It's like git but for servers


 Has anyone else ever had to setup or maintain these sorts
of servers? I know one of the things when I did this was I had
_the wiki page_ that tracked the server configuration. Do you know what
I'm talking about. The way it would work is I'd run a command on
the server and then copy and paste it into the wiki.
Then you should be able to just re-run all the commands on the
wiki page if you ever had to set up another server. Right?
It all worked just fine until it didn't. I've tried lot's of things,
wiki pages, storing configuration files in source control. Virtual
machine snapshots.

What I really wanted was _git but for servers_

----
### Key Component: Image

**Image** is a readonly template that contains your operating system files.
You can create new images by commiting your changes to a container.

----

### [Demo 2](https://github.com/chrisortman/docker-demos/blob/20150601-crineta/demo2.sh) - create image from container

Remember my `run` command?

> When I say run I'm telling docker to go get the ubuntu image and
> create and start a new container from it. Then map port 8080
> of my host to 8080 of my container

Let's use that again, but this time we'll just start with a plain
ubuntu image.

```
docker run -it ubuntu /bin/bash
```

So here I am, I'm in an ubuntu VM.

Fast yes?

Now, if I make changes here, exit and then return you'll see
my changes are discarded. That's because run starts from the image
and you have to explictly commit changes to your image.

```
echo "Hello World" > /home/foo.txt
cat /home/foo.txt
exit
docker run -it ubuntu /bin/bash
cat /home/foo.txt
echo "Hello World" > /home/foo.txt
cat /home/foo.txt
exit
```

So to commit my changes I need to find my container ID.

Docker has a `ps` command that will show you all running containers

```
docker ps
```

But this only shows _running_ containers, and our container is stopped.

To see the most recently created container I can use the `-l` option

```
docker ps -l
```

And if I want just the container ID I can use `-q` with it.

```
docker ps -lq
```

And then combine those in order to commit my change

```
docker commit -a 'Chris Ortman' -m 'Add foo' $(docker ps -lq) demo:latest
```

And now if I create a container based on my new image I can see my change

```
docker run -it demo:latest /bin/bash
cat /home/foo.txt
exit
```

It's a bit like you do in git where no one can see your changes
until you commit and push them.

Unfortunately viewing the commit messages isn't a super great experience,
which if you remember some of the early days of git commands well...

The best I could find to show them is this command

```
docker images -a --no-trunc | head -n4 | grep -v "IMAGE ID" | awk '{ print $3 }' | xargs docker inspect | grep Comment
```

which I got from this [stack overflow answer](http://stackoverflow.com/questions/26199903/how-to-see-commit-message-from-docker-images)

If I wanted to share my changes with
you, I would _push_ my image to a registry.



## My setup documentation is executable

This is so much better than virtual machine snap shots. And is
really helpful for keeping track of changes. But, it is still
a bit cumbersome if I am not working completely linearly. If
I need to make a change to an earlier step I would have to remember
all the subsequent steps and run them manually.

Remember when we were talking about _the wiki page_?

Wouldn't it be great if the documentation was just the instructions?

Isn't this kind of what we strive for with our programs?

This is what Dockerfiles are for.


----
### Key Component: Dockerfile

*Dockerfile* is a Makefile for your image. Contains the steps needed to produce the container.

----

So at UI, we maintain several rails applications. What's really awesome
is that they are very thoroughly tested. What's not so awesome is that a
lot of the tests are full browser acceptance selenium tests.

This means they are slow

Two and a half to three hours slow.

What's worse is when they are running you can't really use your computer
because firefox instances will randomly pop up in front of you and if
you happen to click on the wrong thing you can fail the test.

I need a way to run these tests on another server or in the background so
I can continue working while they run.

On our build servers we use this thing called `xvfb` which is basically
a fake display. We then point firefox to use this fake display and vioala
no GUI needed.

The trouble is that this only works on linux.

The reason that is trouble is that it makes it hard for me to just
give everyone a script or program to run because it will require
some system setup and configuration.

What I want is a docker image that has ruby, firefox, and xvfb all set
up and ready to go. Then I can give it to each team they can build on
that with their application code and we can run the entire cucumber suite
with one command.

### [Demo 3](https://github.com/chrisortman/cucumber-hydra/blob/master/cucumber-runner/Dockerfile) - review rvm-base Dockerfile
  * FROM defines base image
  * Each line causes a commit
    * This can cause problems with apt-get
  * Because each commit is an image / layer I want to combine commands
  * Layer's get reused (SHA)
  * ADD is used to add local files, can also accept a url
  * ENV variables
  * switch to non root user
  * ONBUILD will run commands when someone uses my image

## I can keep all the different development tools I want to try isolated from the rest of my system (and eachother)

How important is it for us to stay up to date on new technologies?

How big of a pain in the ass is it to have to install / uninstall beta versions of  visual studio?

What if you work on wordpress? Do you really like messing sith your system apache & php on OSX?

Want to try redis, mongo, rethinkdb, mysql, postgres? Want them to alawys keep starting up
with your computer when you turn it on? Because you used it for one demo / tutorial?

Of course not. Well, we can create containers for all these things, compose them together and just delete the container when we are done.

----
### Key Component: Container

*Container* is your virtualized operating system. It uses namespaces,
control groups, and union file systems to provide isolation for
applications

----

So what's making all this happen.

We're familiar with virtualization technologies? VMWare, Hyper-V, VirtualBox, Parallels?

We call these things virtual _machines_, because they let me create new _machines_ using software. Our operating systems, windows, mac, linux only know how to talk to components such as hard drives, CPU's and memory, so if I am to create a machine purely in software then I have to have software make it look like there is a hard disk or CPU when there really isn't one. Because this is in face a real CPU that my virtual machine is using to create a virtual CPU we say that we virtualizing the hardware, so this is called *hardware virtualization*

Docker on the other hand, virtualizes at the operating system level. So you get your own file system, but not hard disk. You can't see processes in other containers, but you see the same physical CPU

For example in my docker host I have 6.9 GB of free disk space

```
docker@boot2docker:~$ df -h
Filesystem                Size      Used Available Use% Mounted on
tmpfs                     1.8G     94.5M      1.7G   5% /
tmpfs                  1002.1M    324.0K   1001.8M   0% /dev/shm
/dev/sda1                18.2G      6.8G     10.4G  40% /mnt/sda1
cgroup                 1002.1M         0   1002.1M   0% /sys/fs/cgroup
none                    464.8G    299.6G    165.1G  64% /Users
/dev/sda1                18.2G      6.8G     10.4G  40% /mnt/sda1/var/lib/docker/aufs
none                     18.2G      6.8G     10.4G  40% /mnt/sda1/var/lib/docker/aufs/mnt/431abd58c10b5560fee38b4dab8f6121f2f5b3f9475298e90b184caa423dd369
none                     18.2G      6.8G     10.4G  40% /mnt/sda1/var/lib/docker/aufs/mnt/2342b6aa2e395dd9dd
```

And if I run this command in 2 of my containers

jira

```
root@2342b6aa2e39:/# df -h
Filesystem      Size  Used Avail Use% Mounted on
none             19G  6.9G   11G  40% /
tmpfs          1003M     0 1003M   0% /dev
shm              64M     0   64M   0% /dev/shm
/dev/sda1        19G  6.9G   11G  40% /etc/hosts
tmpfs          1003M     0 1003M   0% /proc/kcore
tmpfs          1003M     0 1003M   0% /proc/timer_stats

```

teamcity

```
daemon@431abd58c10b:/var/local/atlassian/jira$ df -h
Filesystem      Size  Used Avail Use% Mounted on
none             19G  6.9G   11G  40% /
tmpfs          1003M     0 1003M   0% /dev
shm              64M     0   64M   0% /dev/shm
/dev/sda1        19G  6.9G   11G  40% /etc/hosts
```

You can see that they can both see /dev/sda1 and it's size but
they would have different file contents at the same path

It is doing this by using something called a union file system. I starts
with a common base (say a default install of ubuntu) and mounts that in
read only mode. It then sticks a writable layer on top of that and any
changes that you make it copies out of the read only layer and into the
writable one. If you're familiar with how fork works at the process level,
it is a similar concept.







### [Demo 4](https://github.com/chrisortman/docker-demos/blob/20150601-crineta/demo4.sh), mysql, php

Now anytime you have data that is going to be generated at runtime
that you want persisted, you will want to store it in a _data volume_

_Containers_ are meant to be portable, but when you say save, you want
to know where that data is and that it isn't going anywhere.

There are different kinds of _data volumes_

* standard - mounts storage from the host into the container, bypassing the union file system. You can see the host path using `docker inspect`
* host mounted - Let's you specify a path on your host to mount into the container. This works through boot2docker via sharing in virtualbox. Useful for mounting your application code
* data volume container - This is a container that is solely for the purpose of mounting volumes. The usefulness of this is that it makes it easy to share the directory between containers. This is the recommended way for dealing with databases.

Create a container just for mysql data. The mysql image is preconfigured to
use `/var/lib/mysql` to write data to (this is mysql default). In this container I'm declaring that that particular directory should be a _volume_

```
docker create -v /var/lib/mysql --name=mysql-data mysql:5.6 /bin/true
```

Let's see what is in our `/var/lib/mysql` dir

```
docker run --volumes-from=mysql-data ubuntu /bin/bash -c "ls /var/lib/mysql;echo done"
```

Nothing

This container can't really do anything by itself. I need to start another
container that will share this _volume_

Now I can create a mysql database container that uses this volume

```
export SILLY_PASSWORD=welcometothejungle

docker create --name mysql-db --volumes-from=mysql-data -e MYSQL_ROOT_PASSWORD=$SILLY_PASSWORD -p 3306:3306 mysql:5.6

docker start mysql-db

docker run --volumes-from=mysql-data ubuntu /bin/bash -c "ls /var/lib/mysql;echo done"
```

So now mysql has initialized itself in my data volume.

Because I started my mysql container and exposed the port I can connect to it from any database client.

```
open -a dbvisualizer --args -connection 'DOCKER MYSQL'
```

And I can create a database here and then see it show up in the data
volume.

When you're dealing with a hosted web / database server, it's really
convenient to be able to work directly with your database.

I don't like the idea of hanging my database out there on the tubes though, so I'll often install `phpmyadmin`

Leaving it installed worries me a little bit because it is then another thing I have to patch and upgrade if there's a security vulnerabililty.

Fortunately we've seen how easy it is to install applications with docker, so I can just run a container with `phpmyadmin` when I need it and shut it down.

Docker will let me link containers together so that I don't have to expose network interfaces out on the host. So what I'm going to do is recreate my
mysql container, but not publish the port this time. Then start a `phpmyadin` container that connects to my database

```
docker stop mysql-db
docker rm mysql-db
docker create --name mysql-db --volumes-from=mysql-data -e MYSQL_ROOT_PASSWORD=$SILLY_PASSWORD mysql:5.6
docker start mysql-db
open -a dbvisualizer --args -connection 'DOCKER MYSQL'
docker run -d --link mysql-db:mysql -e MYSQL_USERNAME=root --name phpmyadmin -p 8001:80 corbinu/docker-phpmyadmin
open -a firefox http://$(boot2docker ip):8001
```

### Wrap-up

#### What have we learned

* Download and run pre-configured applications
* Create images and share them
* Persist data
* Connect containers

#### Four key components

#### More Info

* join #docker channel on slack [http://j.mp/tc-slack]()


