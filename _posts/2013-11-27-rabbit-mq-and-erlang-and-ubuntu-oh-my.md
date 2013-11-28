---
layout: base
title: RabbitMQ and Erlang and Ubuntu (12.04) Oh My!
show_title: true
---

As part of an upcoming development effort for a new client, I am looking at
using a message broker to facilitate inter-app communications. The use case
is that the new system integrates several different data sources into a
consolidated view.

For example: When changes are made to the canonical source in postgres,
we need to let the our indexer service know that it needs to update the
elasticsearch index so the front-end application can serve the freshest data.

## The Problem
In 2 words: Unmet Dependencies. Rabbit MQ is built with
[Erlang](http://erlang.org). The Erlang packages in Ubuntu 12.04 (Precise) are
out of date or not present. I downloaded the [RabbitMQ Ubuntu Debian package](http://www.rabbitmq.com/install-debian.html)
and expected a quick dpkg -i install. However, instead of a quick list of
additional packages that would be installed and a friendly "Y/n?", I got:

    sudo dpkg -i rabbitmq-server_3.2.1-1_all.deb
    ...stuff...
    dpkg: dependency problems prevent configuration of rabbitmq-server:
     rabbitmq-server depends on erlang-nox (>= 1:13.b.3) | esl-erlang; however:
      Package erlang-nox is not installed.
      Package esl-erlang is not installed.
    dpkg: error processing rabbitmq-server (--install):
    ...stuff...
    Errors were encountered while processing:
     rabbitmq-server

So. I tried installing Erlang from the Ubuntu packages and got this:

    sudo apt-get install erlang-nox esl-erlang
    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    Package esl-erlang is not available, but is referred to by another package.
    This may mean that the package is missing, has been obsoleted, or
    is only available from another source
    E: Package 'esl-erlang' has no installation candidate

<div style="text-align: center">
  <img src="http://i.qkme.me/MR.jpg" alt="Wat do?"/>
</div>

## The Solution

Go to [erlang.org](http://erlang.org). Find the [download page for Ubuntu](https://www.erlang-solutions.com/downloads/download-erlang-otp#ubuntu)
which is NOT at erlang.org - it is at [www.erlang-solutions.com](http://www.erlang-solutions.com). Craziness. I
follow the directions and add this line to my `/etc/apt/sources.list`

    deb http://packages.erlang-solutions.com/debian precise contrib

then

    sudo apt-get update

then

    sudo apt-get install erlang

then... I still had a problem. The first time I dpkg-ed the rabbitmq-server, it
added it to invoke-rc. So when I tried to install erlang, it gave me this:

    rabbitmq-server : Depends: erlang-nox (>= 1:13.b.3) but it is not going to be installed or
                            esl-erlang but it is not going to be installed

so to get rid of the invalid package, I

    sudo dpkg -r rabbitmq-server

then

    sudo apt-get install erlang

Success was in my grasp! However tantilizingly close it may be, however... It
still eluded me.

When I tried to install the package again, I got this nasty little message:

    sudo dpkg -i rabbitmq-server_3.2.1-1_all.deb
    ...stuff...
    dpkg: dependency problems prevent configuration of rabbitmq-server:
     rabbitmq-server depends on erlang-nox (>= 1:13.b.3) | esl-erlang; however:
      Package erlang-nox is not installed.
      Package esl-erlang is not installed.
    dpkg: error processing rabbitmq-server (--install):
     dependency problems - leaving unconfigured
    ...stuff...
    Errors were encountered while processing:
     rabbitmq-server

<div style="text-align: center">
<img alt="head banging on desk" src="http://25.media.tumblr.com/tumblr_lej9mdqBSZ1qdltfjo1_400.gif"/>
</div>

so I looked more closely and saw that I could install erlang-nox (which is a
package for erlang for stuff that doesn't require X to be running -get it? "no X").
So I gave it a shot.

    sudo apt-get install erlang-nox

It worked.

    sudo dpkg -i rabbitmq-server_3.2.1-1_all.deb

And I was finally greeted with this little beauty:

    Selecting previously unselected package rabbitmq-server.
    ...stuff...
     * Starting message broker rabbitmq-server [OK]

#### Victory was mine.

<div style="text-align: center">
<img alt= "Success Kid resolves dependencies, neckbeard increaseth" src="http://i.imgur.com/1TWj3tB.png"/>
</div>

## TLDR

download rabbitmq-server debian package from [here](http://www.rabbitmq.com/install-debian.html)
but DO NOT run it yet.

add this line to your `/etc/apt/sources.list`

    deb http://packages.erlang-solutions.com/debian precise contrib

then

    sudo apt-get update

then

    sudo apt-get install erlang

then

    sudo apt-get install erlang-nox

then

    sudo dpkg -i rabbitmq-server_3.2.1-1_all.deb

then have a hot chocolate. You've earned it.
