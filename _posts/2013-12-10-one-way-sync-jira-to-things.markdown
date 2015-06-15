---
layout: post
title: "One Way Sync Jira to Things"
modified:
categories: tools
excerpt: Little ruby script to take jira tickets and create todo item's Things.
tags: [ruby, osx]
image:
  feature:
---

Little ruby script to take the results of a jql query (advanced search) in jira and create todo item's for Cultured Code's Things App. I use it at the beginning of a sprint to keep track of my own work and progress. If I get less lazy I could always sync back in the other direction.

{% gist 7902002 config.yaml %}

{% gist 7902002 jira_to_things.rb %}