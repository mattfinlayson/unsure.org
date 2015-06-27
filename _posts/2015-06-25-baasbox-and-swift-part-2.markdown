---
layout: post
title: "BaasBox and Swift - Part 2"
modified:
categories:
excerpt: "Implementing the BaasBox tutorial in Swift"
tags: [swift, ios, baasbox]
image:
  feature:
date: 2015-06-25T10:08:25-07:00
---

In my [first post](https://unsure.org/baasbox-and-swift/) I stepped through the process of porting the skeleton of the BassBox tutorial from Objective-C to Swift. This post takes that project and implements the actual tutorial in Swift. The completed project can be found [on this github branch](https://github.com/savagegus/DearDiary-iOS-starter/tree/tutorial).

####The Model

The model is a very basic class with two required fields, the title and body which are both strings. We initialize it by overridding initWithDictionary. The main Swift-ism here is using optional binding to verify the dictionary contains the key / value pairs we expect.

{% gist savagegus/7cfd8b5ddedde896e1ba SMPost.swift %}

When I have multiple values I need to verify through optional binding I do like using the shorthand syntax of using a single **if let** statement, remember through it's only appropriate if you're looking for a single outcome, if you want to handle multiple combinations (title is set but body is not, etc) you should look at a [switch statement](http://natashatherobot.com/swift-2-pattern-matching-unwrapping-multiple-optionals/).

####SMLoginViewController

The tutorial has you copy a completed [LoginViewController](https://github.com/baasbox/DearDiary-iOS/blob/master/DearDiary/SMLoginViewController.m) from the finished project. It's a rough implementation which uses segmented buttons to toggle between signup and login in code rather than through a storyboard, but it works so for now, let's convert it to swift.

My first step is to build a class that matches the Objective-C version, then fill in the details. To do this I first create a create a new class in Xcode by going to File -> New -> File -> iOS Source -> Cocoa Touch Class. Then I name the class, choose the appropriate subclass, and choose Swift as the language.

Next I open the original header file, the header defines our properties and methods and gives us a quick way to build our skeleton class. Briefly this:

{% gist savagegus/7cfd8b5ddedde896e1ba SMLoginViewController.h %}

Becomes this:

{% gist savagegus/7cfd8b5ddedde896e1ba SMLoginViewController.1.swift %}

Now we need to fill in our instance menthods and implement our required methods for subclassing the UIViewController. The changes were very straight forward with the exception if implementing initWithNibName. The comparable swift method was easy to write:

{% highlight swift %}
override init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: NSBundle?) {
    super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil);
}
{% endhighlight %}

However, Xcode gave me the following error.

> SMLoginViewController.swift:16:1: 'required' initializer 'init(coder:)' must be provided by subclass of 'UIViewController'

The fix was easy, Xcode was able to generate it, and I was able to find the explaination of what was going on at [stackoverflow](http://stackoverflow.com/questions/24036393/fatal-error-use-of-unimplemented-initializer-initcoder-for-class)

> Objective-C automatically inherits initWithCoder that's why we don't need to add it to destinationViewController.

>Swift requires adding init(coder aDecoder: NSCoder!) if you have init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: NSBundle?) on your destinationViewController.

####Authentication
Moving back to the tutorial, the next step is to perform the the first request to BaasBox. This is done in the viewWillAppear method of the MasterViewController. Once again the Swift implementation was just different syntax with one exception. The call to BaasBox for a collection of SMPosts is done with a completion and it took me a little time to work out the correct syntax.

{% gist savagegus/7cfd8b5ddedde896e1ba getObjectsWithCompletion.m %}

With the help of [fuckingclosuresyntax](http://fuckingclosuresyntax.com) this becomes:

{% gist savagegus/7cfd8b5ddedde896e1ba getObjectsWithCompletion.swift %}

Also don't forget inside a closure you must use self where appropriate. This same pattern is repeated for authenticateUser in SMLoginViewController and createNewPost / savePost in SMMasterViewController.

This takes us through the whole tutorial for the DearDiary getting started app.
