---
layout: post
title: Key listing support in Consul client
excerpt: "Consul seems to only store strings, this is how I worked around it."
modified: 2014-08-06
tags: [devops, python]
comments: false
image:
  feature:
---

For integration between ansible and consul I've been using a third party python client called [consulate](https://github.com/gmr/consulate). It is decent, however both it and consul are new and it doesn't support the full consul HTTP API yet.

Currently I'm trying to model our topology in consul's key value store but lists of values are not intuitive. Consul seems to only store strings, so without doing string parsing / casting I am unable to get complicated values out.

For example, I'd like to store something like this:

/roles/zookeeper/zones = ['us-west-2a', 'us-west-2b', 'us-west-2c'] 

and when I do a query against consul I could get back a list to work with. Since I can't I had been doing terrible things like this:

{% highlight python %}
def get_keys(self):
        output = {}
        for k, v in self.session.kv.items().iteritems():
            if not v is None and type(v) is str and v.startswith("[") and v.endswith("]"):
                output[k] = v.replace('[', '').replace(']', '').split(', ')
            else:
                output[k] = v
        return output
{% endhighlight %}


Reading the [Consul API docs](http://www.consul.io/docs/agent/http.html) closer though I found that they have a weird implementation to support this. Specifically:

> It is possible to also only list keys without their values by using the "?keys" query parameter along with a GET request. This will return a list of the keys under the given prefix. The optional "?separator=" can be used to list only up to a given separator.
>
> For example, listing "/web/" with a "/" seperator may return:
> 
> [
> "/web/bar",
> "/web/foo",
> "/web/subdir/"
> ]
> Using the key listing method may be suitable when you do not need the values or flags, or want to implement a key-space explorer.

So I dug into the (simple) consulate find method and added support for ?keys to it. [The pull request](https://github.com/gmr/consulate/pull/5) logged with consulate and we can use my consulate fork for the time being.
