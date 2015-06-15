---
layout: post
title: Dynamic inventory and variables in Ansible
excerpt: "Use consul as a facts source and use that to drive role behavior."
modified: 2014-08-06
tags: [devops, python]
comments: false
image:
  feature: sample-image-5.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
---
I've been building out automation for deploying micro services in ec2. We're using [consul](http://consul.io) for service registration, discovery, health checks, and configuration. Since consul provides an available key value store for configuration we've been trying to define the topology that way. Ansible has some very good documentation and it is one of the things I like most about the project. 

Documentation for building your own [dynamic inventory](http://docs.ansible.com/developing_inventory.html) is fairly complete, but I was having trouble with including variables in that inventory. The dynamic inventory documentation shows examples of of host level variables in it’s json output:

eg:

{% highlight json %}
{    
  "databases": {        
    "hosts"   : [ "host1.example.com", "host2.example.com" ],        
    "vars"    : {            
      "a"   : true        
    }    
  }
}
{% endhighlight %}


However, in the standard inventory documentation there is a differnt type of variable, specifically, [group variables](http://docs.ansible.com/intro_inventory.html#group-variables).

{% highlight yaml %}
[atlanta]
host1
host2

[atlanta:vars]
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com
{% endhighlight %}


So my assumption was I could use the group variable syntax in the dynamic inventory output to achive the same thing, the power here was that different consul instances could contain different values allowing me to build a fairly dynamic infrastructure. Combining the documentation from the static inventory with the dynamic output gave me something that looked like this:

{% highlight json %}
{    
  "databases": {        
    "hosts"   : [ "host1.example.com", "host2.example.com" ],        
    "vars"    : {            
      "a"   : true        
    }    
  },
  "databases:vars": {
    "postgres": {
       "version": "9.3"
     } 
  }
}
{% endhighlight %}

Unfortunately ansible-playbook wanted nothing to do with this. 'databases:vars' was being cast to a list and treated as a group which was stomping the variables I was trying to pass around. 
I spent a while thinking about the problem and decided that inventory wasn't actually where I needed these variables passed in. Instead it would be fine to use consul as a facts source and use that to drive role behavior. I started out trying to augment the magic variable 'ansible_facts' by modifying the [setup module] (https://github.com/ansible/ansible/blob/devel/library/system/setup) but ultimately I didn't want to maintain my own core module. Instead I was able to find two good blog posts about writing a fact gathering module. The first is a little old but comes from one of the best ansible blogs I've found. [Ansible: variables, variables, and more variables](http://jpmens.net/2012/08/30/ansible-variables-variables-and-more-variables/) explains the basic approach well but the example code seemed to no longer work. [Docker: Containers for the Masses -- The docker_facts module](http://patg.net/ansible,docker/2014/07/10/ansible-docker-facts/) is a much more recent post and had a working [example](https://github.com/CaptTofu/ansible/blob/docker_facts/library/cloud/docker_facts) to go along with it. 
It turned out the reason the first example wasn't working was due to how the module was exiting. The first example was just printing the output, now the correct approach is to use the module.exit_json method.

{% highlight python %}
module.exit_json(changed=changed, ansible_facts=ansible_facts)
{% endhighlight %}

I've posted the module on my [ansible fork](https://github.com/savagegus/ansible/blob/consul_facts/library/cloud/consul_facts) and may [sent a pull request over](https://github.com/ansible/ansible/pull/8477). 