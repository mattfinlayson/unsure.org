---
layout: post
title: "BaasBox and Swift - Part 1"
modified:
categories: 
excerpt: "Porting BaasBox tutorial from Objective-C to Swift"
tags: [swift, ios]
image:
  feature:
---
[BaasBox](http://www.baasbox.com) is a tool that allows you to quickly build a backend for an application. The getting started guide for using BaasBox is a little dated and written entirely in Objective-C. The tutorial is fine, but since I'm working in Swift day to day the application I was looking to build would also be in Swift. 

My first thought was to just google around and find newer tutorials. I found a single post that seemed relevant, [Swifing with BaasBox - Building a quiz app with BaasBox](http://www.chupamobile.com/tutorial-ios/swifting-with-baasbox-597 ) Rather than lose all hope I decided that porting the tutorial app to Swift might be a good exercise in BaasBox and might give me an idea if I liked the tool.

I cloned the [starter project](https://github.com/baasbox/DearDiary-iOS-starter)  and created a [swift branch](https://github.com/savagegus/DearDiary-iOS-starter/tree/swift) started hacking away.

Next was the brute force method of including the BaasBox SDK:

1. [Downloaded the SDK](https://github.com/baasbox/iOS-SDK/archive/v0.9.0.zip) and dragged the "BaasBox-iOS-SDK" folder onto the root of the project.  
2. Created a bridging header to allow swift to import Objective-C source. Go to *File -> New -> File -> iOS -> Header File*. The naming is very specific, it should be *'[ProjectName]-Bridging-Header.h'*.
3. In Swift files you can now *import BaasBox*

{% gist savagegus/84d1d30f9e9af4db63ac %}

The latest SDK and the skeleton project were on different versions so I needed to change some of the tests to get the project to compile.

From there I stepped through and converted each class from Objective-C to Swift. They were straight forward and I was expecting more difficulty. This gets us to the beginning of the tutorial, in the next post we'll actually implement the tutorial in Swift.