---
layout: post
title: "Getting Tumblr to Octopress Working"
modified:
categories: In which our hero struggles with YAML
excerpt:
tags: [ruby]
image:
  feature:
---

Following [this script](https://github.com/mojombo/jekyll/wiki/blog-migrations) to get posts from tumblr. I then get YAML errors due to bad characters in the title sections. A little grep and awk shows me the bogus titles.

{% gist 1553113 error1 %}