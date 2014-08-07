---
title: 5 Ways To Improve Your RubyMotion Apps Organisation
layout: post
---

## 1. A Good Folder Structure

This may be an obvious one, but I've seen some projects that are in shambles and it came mainly from not having set folders to put certain kinds of files in.

I put all my images, storyboards, and such into the `resources` folder, schemas for [CDQ/ruby-xcdm](https://motioninmotion.tv/screencasts/5) in the default `schemas` folder, then in the `app` folder, I have `controllers`, `models`, `views`, `delegates`, and `data_sources` folders.

Those last two I don't usually see, and explain why I have them with...

## 2. Extract Your Delegates and Data Sources

Something I've been doing recently to keep my controllers clean and focused is putting my delegate and data source methods into separate modules, and including them in the controller.

This keeps the controller focused on setting up connections and fairly basic things, while all those extra methods that usually make controllers stupidly massive, are grouped together in the one file for each delegate or data source (including ones I create myself).

For example, if I had a `PostsController` which was a `UITableViewController`, and also used alert views, I would have three modules, `PostControllerTableViewDataSource`, `PostControllerTableViewDelegate`, and `PostControllerAlertViewDelegate`. Then it's simple to include them in the controller using `include`.

The added bonus is if I make them generic enough, I can use them in multiple controllers, or port them over easily to another app.

## 3. No Spaghetti Code

Another obvious one, but I see most people do this, keep the code for your views, controllers, and models separate, and follow Apple's way for doing MVC.

![Diagram](/assets/0R251I3m0a0N3a2s3b3h3G2a1x0y460w.png)

Let your controller call methods on your views and models, but setup ways for them to tell your controller something. For models, `NSNotificationCenter` and [Key-Value Observing](https://developer.apple.com/library/mac/documentation/cocoa/conceptual/KeyValueObserving/KeyValueObserving.html) is usually the way to go, and for views, you can choose between delegates, data sources, and target action.

I've got two whole episodes dedicated to explaining this. [Delegates & Data Sources](https://motioninmotion.tv/screencasts/2) and [More Delegates, KVO, and Target Action](https://motioninmotion.tv/screencasts/3).

Having things correctly separated, and using the correct ways of communication makes testing easier, and makes updates easier, especially if you're going from iPhone only to a universal app.

## 4. Only Require What You Need

This isn't necessarily for code organisation, but it is something I see people doing a lot. If you're using BubbleWrap or other large libraries, don't just let bundler require it all, because you're adding a very very large amount of bloat to your apps compiled size.

I usually will remove the bundler code from my `Rakefile` when I start an app, because it then forces me to think about what I actually need, and only require it when I'm going to use it.

With just a few of these large libraries, you will start pushing size limits for non-wifi downloads.

## 5. Interface Builder is your friend

Last one, and again a persistent problem I see, is people avoiding Xcode and more to the point, Interface Builder.

Storyboards are a great way to not only remove a lot of bloat from your code, but also as a communication tool between you and designers or clients.

You can also take advantage of AutoLayout a bit easier, which is always nice, because even though motion-layout makes it very easy to setup basic layouts, our apps don't always fit within the limitations.

I suggest you get to know how to pragmatically build views, but don't neglect to use Interface Builder.

## Your thoughts?

If you have some ways of organising your projects that you reckon could be useful for others to know, send me a tweet with your suggestion to [@RubyMotionTV](https://twitter.com/RubyMotionTv) and I'll add a link to the tweet here.