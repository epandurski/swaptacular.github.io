---
layout: post
title: Under the Hood — Running Everything Together
description: >
  Explains how to run a full set of Swaptacular network nodes.
author: Evgeni Pandurski
tags: [under-the-hood]
---

In [the previous post](/2023/10/27/under-the-hood-payment-requests/)
of the "Under the hood" series, I explained how payment requests work.
In this post, I will show you how to install and run the full set of
[Swaptacular network nodes](/overview/) on your laptop.

**A fair warning:** This post may become too technical for the taste
of some readers.

<!--more-->

## Preparing your machine

Because all of the services that we will run here are packaged as
[docker images](https://www.geeksforgeeks.org/what-is-docker-images/),
chances are that you will be able to run them on Linux, Windows, or
Mac (currently we have only x86-64 images). However, the instructions
given bellow are for Linux, because I use Linux, and this is the most
popular platform for deployment of servers.

You need to install three things on your machine:

1. [Docker Engine](https://docs.docker.com/engine/)
2. [Docker Compose](https://docs.docker.com/compose/)
3. [Git](https://git-scm.com/)

It should be relatively easy to find detailed instructions on how to
install those for most operating systems.

## Cloning the Git repositories

We want to run 3 different Swaptacular nodes simultaneously:

1. an *accounting authority* node,
2. a *creditors agent* node,
3. a *debtors agent* node.

The source code for each one of these nodes resides in a separate Git
repository. You need to clone each of those 3 repositories separately:

{% highlight shell_session %}
$ git clone https://github.com/swaptacular/swpt_accounts.git
Cloning into 'swpt_accounts'...

$ git clone https://github.com/swaptacular/swpt_creditors.git
Cloning into 'swpt_creditors'...

$ git clone https://github.com/swaptacular/swpt_debtors.git
Cloning into 'swpt_debtors'...

$ ls
swpt_accounts  swpt_creditors  swpt_debtors
{% endhighlight %}

## Starting the nodes

Open a new terminal for the **accounting authority** node, and in it,
execute the following commands:

{% highlight shell_session %}
$ cd swpt_accounts/
$ cp development.env .env
$ docker-compose -f docker-compose-all.yml up --build
Creating network with the default driver
Building accounts-server
...
{% endhighlight %}

Then, open another terminal for the **creditors agent** node, and in
it, execute the commands:

{% highlight shell_session %}
$ cd swpt_creditors/
$ cp development.env .env
$ docker-compose -f docker-compose-all.yml up --build
Creating network with the default driver
Building creditors-server
...
{% endhighlight %}


Finally, open a third terminal for the **debtors agent** node, and in
it, execute:

{% highlight shell_session %}
$ cd swpt_debtors/
$ cp development.env .env
$ docker-compose -f docker-compose-all.yml up --build
Creating network with the default driver
Building debtors-server
...
{% endhighlight %}

In each of those 3 terminals, a new docker image will be built from
source, and then a bunch of docker containers will be started, running
the image, along with a bunch of other docker images, downloaded from
Internet. The newly created `.env` files contain basic configuration
settings.

**Note:** You can press `Ctrl-C` in the terminal, to stop all running
containers.

Do not be alarmed by the large amount of spewed log messages. A lot of
things need to happen before everything is configured and ready to go!
In particular, you will probably see a lot of `Connection refused`
error messages. This happens because every node is constantly trying
to connect to its peer nodes, which will continue to fail until all
peer nodes are up and running. Also, you may periodically see `missed
heartbeats from client, timeout` error messages from the
[RabbitMQ](https://www.rabbitmq.com/) server. This is perfectly
normal.

## Testing everything together

Before you begin experimenting with the new setup, you need to add the
line:

> 127.0.0.1 host.docker.internal

to the *hosts file* on your machine. On Linux, you can do this by
executing the following command:

{% highlight shell_session %}
$ sudo sh -c 'echo "127.0.0.1 host.docker.internal" >> /etc/hosts'
{% endhighlight %}

After you have successfully done this, you can use the following
links:

### [Debtors agent's "My Currency" webapp](https://host.docker.internal:44302/debtors-webapp/)

Use this to create new currencies.

### [Debtors agent's fake email server](http://localhost:8026/)

You will need this in order to read the email messages which the
*debtors agent* sends to you, during the user registration and login.

### [Creditors agent's "My Wallet" webapp](https://localhost:44301/creditors-webapp/)

Use this to obtain already created currencies, trade with them, and
make payments with them. By default, the creditors agent is configured
to run [automated currency exchange
sessions](/2024/07/04/automated-currency-exchanges/) every 10 minutes.
This is convenient for testing.

### [Creditors agent's fake email server](http://localhost:8025/)

You will need this in order to read the email messages which the
*creditors agent* sends to you, during the user registration and
login.

## Conclusion

In this post, I explained how one could easily run the full set of
Swaptacular network nodes (that is: accounting authority, creditors
agent, and debtors agent nodes) on a single computer, for testing and
evaluation purposes.
