---
layout: post
title: "404 Error when trying to rebuild a docker image"
comments: true
tags:
 - docker
---

TLDR; When you put your docker file together make sure that you have all your `apt-get install` commands together

I have a docker file that I use to create a container for running headless cucumber tests against our rails apps. It looked something like this

```

RUN apt-get update
RUN apt-get upgrade --assume-yes

RUN apt-get install --assume-yes cups libcups2-dev build-essential \
                       freetds-dev freetds-bin tdsodbc \
                       unixodbc unixodbc-dev \
                       openssh-server git-core openssh-client curl vim \
                       build-essential \
                       openssl libreadline6 libreadline6-dev curl \
                       zlib1g zlib1g-dev libssl-dev libyaml-dev \ 
                       libsqlite3-dev sqlite3 libxml2-dev libxslt-dev \
                       autoconf libc6-dev ncurses-dev automake \
                       libtool bison pkg-config gawk libgdbm-dev libffi-dev nodejs


ADD scripts/xvfb /etc/init.d/xvfb
# make sure we get right timezone
RUN echo America/Chicago > /etc/timezone && dpkg-reconfigure --frontend noninteractive tzdata

RUN useradd -ms /bin/bash appuser
USER appuser

# install rvm with ruby 1.9.3
RUN gpg --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3
RUN \curl -L get.rvm.io | bash -s stable --autolibs=read-fail
RUN /bin/bash -l -c "rvm install ruby-1.9.3-p547"
RUN /bin/bash -l -c "rvm install ruby-2.1.5"
RUN /bin/bash -l -c "echo 'gem: --no-ri --no-rdoc' > ~/.gemrc"
RUN /bin/bash -l -c "gem install bundler --no-ri --no-rdoc"

#------------------ FIREFOX ------------------------

RUN apt-get install -y xfonts-100dpi xfonts-75dpi xfonts-scalable \
                       xfonts-cyrillic xvfb x11-apps  imagemagick \
                       libasound2 libgtk2.0-dev

ADD firefox-24.8.1esr.tar.bz2 /opt/
RUN ln -s /opt/firefox/firefox /usr/bin/firefox

# Export display variable for communication between xvfb and firefox
ENV DISPLAY :99
#------------------------------------------------------
```

I needed to add an additional version of ruby to this image, so I added another RUN line

```
RUN /bin/bash -l -c "rvm install ruby-1.8.7-p374-cheerful0"
```

I put this line before my ruby-1.9.3 line and tried to rebuild my image.

When I did I got a whole bunch of 404 not found archive.ubuntu.org errors. This seemed strange to me as I couldn't imagine the ubuntu 12.04.5 repositories had suddenly gone away. The problem was that my sources were out of date

To understand why we need to understand how a docker file works.

When a docker image is built each line of the file is executed and then _committed_. SHA1's are generated for each command. Each subsequent command gets its own SHA and knows what its parent command|SHA was. Remind you of [anything](http://git-scm.org)

When an image is rebuilt it can skip commands it has already run and just use the built image from that command. So if I added a line above my install ruby-1.9 line then that will be the first line that actually gets executed. Since that line changes, all the subsequent commands need to be run again. This means that my apt-get install commands for those fonts will run again, but without having first run apg-get update

To remedy this we need to do two things. 

First to prevent this problem in the future, move the firefox stuff before the RVM stuff. This way changing rubies won't cause me to have to rerun apt-get stuff.

Second, we need to work around our current problem. Since my apt-get update line hasn't changed it still won't be run and my apt-get install commands will still fail. You can put comments in your docker file using # and these comments are part of the SHA of your command. This means we can change `apt-get update` to `apt-get update #poop` and now that command will be rerun.


