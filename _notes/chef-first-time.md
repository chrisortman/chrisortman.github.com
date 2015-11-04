My goal is to use chef to provision my server with docker containers.

##Background:

I have a server at digital ocean that I want to run wordpress, minecraft-server and a few random
applications I've developed. So far I've just been manually putting things on there as needed, but
I hate that if something went wrong with that machine I have no way to get it back.

My end vision is an nginx container handling incoming traffic and then proxying to some wordpress
or rails containers. These application containers would have the whole application in them too, so
they probably include nginx or apache. I don't want multiple database processes though (I don't think
anyway) so they probably link to a mysql or postgres container as well.

To setup and configure these containers I want to use chef so I can provision a local VM and test
everything and then run my script again on my production box.

## Chef

My first experience trying to get chef is a little confusing. What I want is to write a script
run a command and have something happen to a virtual machine on my computer.

I found this [learn the basics](https://learn.chef.io/learn-the-basics/ubuntu/) tutorial but
it seems like it wants to install chef *on the VM*

I also thought about maybe I could just pull down a docker image to experiment with, but hard to
pick from this list

```
NAME                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
cbuisson/chef-server                   chef-server with easy access to knife keys...   12                   [OK]
base/chef-server                       Chef 11 server                                  9                    [OK]
okapies/chef-solo                      A docker image based on Ubuntu 12.04 with ...   4                    [OK]
releasequeue/chef-client               Docker image containing Chef client expose...   3                    [OK]
foostan/chef-server-box                Utility tools for serving Chef Server. Inc...   3                    [OK]
jrwesolo/centos-with-chef                                                              2                    [OK]
logankoester/archlinux-chef            Arch Linux with Chef installed from Rubygems    1                    [OK]
linuxkonsult/chef-solo                 An ubuntu image with chef-solo and Berkshelf.   1                    [OK]
swcc/chef                              A docker image container base on ubuntu wi...   1                    [OK]
jrwesolo/ubuntu-with-chef                                                              1                    [OK]
joshuacox/docker-chef-solo                                                             1                    [OK]
jr42/chef-solo-centos-onbuild          Official CentOS base images with current c...   1                    [OK]
jr42/chef-solo-centos                  Official CentOS base images with current c...   1                    [OK]
ryansch/chef-solo-centos               Base image for chef-solo on centos              1                    [OK]
rosstimson/fedora-chef                                                                 0                    [OK]
zuazo/chef-local                       Chef configured to make it easier to run c...   0                    [OK]
jmccann/drone-chef                     Drone plugin for deploying cookbooks to a ...   0                    [OK]
hpess/chef                                                                             0                    [OK]
cato1971/docker-chef-client-personal   Chef Client Personal container for knowled...   0                    [OK]
insaneworks/centos-chef                CentOS 6.6 x86_64 + chef-client                 0                    [OK]
guywithnose/chef                       An image that makes it simple to test chef...   0                    [OK]
urelx/centos-chef-solo                 CentOS base image with chef-solo and berks...   0                    [OK]
lithiumpc/docker-chef-centos7                                                          0                    [OK]
epcim/drone-chef-ci                                                                    0                    [OK]
waldvogel/vagrant-chef-puppet          Vagrant Images for CentOS and Ubuntu with ...   0                    [OK]

```

So in the end I just wound up searching hombrew for chef and found that there is a cask for chef-dk so tried to 
install that because that is just what I normally do.

OK, the _learn the basics_ tutorial mentions that it is just having you do things on your own machine so you can 
learn, so I read through the tutorial and have a basic idea of some of the terms

Eventuall it gets to recipies and I learn that `chef-client` is used to run a recipe, so maybe all I need to
do is slow down a bit and keep reading and it will tell me how to run `chef-client` against a different machine

Bingo, [next tutorial](https://learn.chef.io/manage-a-node/ubuntu/) looks like what I'm after 

So bummer...the tutorial says I *need* a chef server which is what I didn't want. There is a hosted chef server
option, or maybe i can find a container to play that role?

Got through a couple of the tutorials...things are starting to click. It is awesome that Chef let's you have a
VM to develop against.

So now I think what I need is

* Recipe to get docker on a machine
* Recipe to set up the docker registry server
* Recipe to pull an image from my registry server and start it


Working on the docker stuff, I created my own chef repo and cookbook

Following along with [this](https://docs.docker.com/articles/chef/) to set up docker

I finally go to [this](https://learn.chef.io/manage-a-web-app/ubuntu/apply-and-verify-your-web-server-configuration/) part of the 
tutorial and *YES* I can just bootstrap and provision my own local VM




