---
layout: post
title: "Running BaasBox 0.9.4 on Ubuntu 14.04"
modified:
categories: baasbox ubuntu
excerpt: This is a simple guide to getting BaasBox up and running on Ubuntu.
tags: [baasbox, ubunut]
image:
  feature:
date: 2015-07-14T11:38:55-07:00
---

This is a simple guide to getting BaasBox up and running on Ubuntu. I'm running it on a vps on digital ocean. Right now this is purely for a dev environment and you shouldn't expect to use the setup for a production site.

First we get our dependencies installed:
{% highlight bash %}
# Install Java, Supervisor, and unzip
sudo add-apt-repository ppa:webupd8team/java -y
sudo apt-get update
sudo apt-get install oracle-java8-installer unzip supervisor
{% endhighlight %}

Next setup an account to run BaasBox
{% highlight bash %}
# Add baasbox user and set up directories
sudo adduser baasbox
sudo -u baasbox
sudo mkdir -p /opt/baasbox
sudo mkdir -p /var/log/baasbox
{% endhighlight %}

We'll download the latest verison (0.9.4) of BaasBox and symlink it which will be helpful for later upgrades.
{% highlight bash %}
# Download Baasbox
cd /opt
wget --content-disposition http://www.baasbox.com/download/baasbox-stable.zip
unzip baasbox-stable.zip
sudo chown -R baasbox /var/log/baasbox /opt/baasbox-*
ln -s baasbox-0.9.4 baasbox
{% endhighlight %}

We're going to make a couple small changes to the default start script. This will allow us to use a config file for our server and application options, it will also allow us to run BaasBox as a daemon in the next step.
Replace /opt/baasbox/start with the following script:

{% gist savagegus/92ab28f7c41974411dfb start.sh %}

Finally create the server config file at /opt/baasbox/baasbox.conf

{% gist savagegus/92ab28f7c41974411dfb baasbox.conf %}

We'll use supervisord, which we installed in the first step to run BaasBox as a service. Supervisord will be responsible for automatically running BaasBox if the process dies or if the machine is rebooted.
{% highlight bash %}
# Set up supervisor
service supervisor restart

/etc/supervisor/conf.d/baasbox.conf
{% endhighlight %}

Create a config file in /etc/supervisor/conf.d/baasbox.conf

{% gist savagegus/92ab28f7c41974411dfb supervisord-baasbox.conf %}

Now you should be up and running with BaasBox on port 9000. Remember this is only acceptable for development and please replace the default appcode and admin password.
