---
title: A Different Kind of MVC
layout: post
---

## Explaining Cocoa and iOS/OSX Programming Part 1

It's not just Objective-C as a language that can scare people off, RubyMotion helps solve part of the problem, but developing apps for iOS and OSX can be really confusing coming from a background of programming for the web (e.g. with Ruby on Rails).

This series is going to be dedicated to trying to explain some of the confusing parts, those bits Rails, ASP.NET MVC, Django, etc developers get to and either get scared off by or use completely wrong for a year or two until they get that "Ah-ha!" moment, if they haven't given up by then.

**Advertisement for my screencasts**   
[![ad](/assets/1n0o2e3j2V2x342w3E3g3W180F1g2i2m.png)](https://motioninmotion.tv)

## Comparing Apples with Rubys

For anyone that's ever worked with a web framework, this should look pretty familiar. There's a bit more going on than this, but this is the big picture, the stuff we care about 90% of the time.

![Rails-Request](/assets/0L3Z2h1D001q0N3j0A1O000X13440W3I.png)

Let's change the wording to be be more generalised than the web.

![Software-Event](/assets/0N1q122O3D09192y3K262C2A0Y2k3D2f.png)

Ok, so once again a gross oversimplification, but you can see everything directly relating to how a request works in Rails.

## Nice pictures... what are you getting at?

A big problem Rails and other web guys face is trying to compare the idea of how MVC works after years of doing it for one platform (the web) to how MVC works in apps on iOS and OSX.

This is natural, developing for the web vs a piece of software is very different in general, you have to face a bunch of changes in how you think about things, your MVC pattern being one of them.

Controllers and how they interact with the model and view seem to be the big tripping point, but if you just look at them from the right angle, and understand the very basics of how events occur in your applications, it's pretty easy to understand how to use them.

Here is that second image, but this time, we're following the trail of a touch event in an iOS application.

![Touch-Event](/assets/30082n2t023F3d033w441U3n153m270s.png)

Unless you know a bit about iOS programming, I don't expect you to understand this fully, but I'm sure you can understand the general idea.

* The event and event info is the touch and the coordinates of the touch
* The platform is your application, namely the UIApplication object and the run loop, but we'll look into those more another time
* The "software" is your window, the drawing and event handling surface of your application
* The data collected from deciding it was the button that was tapped is
  * What view it was
  * The type of event - touch up (lifting their finger)
  * The configured target object for that event on that button
  * The configured action to perform on the target for that event on that button
* Then just like any other controller in any other MVC pattern, it talks to your models and your views and performs what you told it to

Now this is for target/action, which if you don't know what that is, it's simply what (usually) a controller does when setting up a view to say "hey you, view, when someone taps you, send me this message".

So yes, I may have just thrown around a bunch of words you didn't understand, but all that kind of stuff is covered in so many kinds of introduction iOS and RubyMotion videos, articles, etc.

What I'm trying to achieve is giving you a way, that once you reach the point where you're setting up your first controller, though the ways it's setup is different, you can at least work out what's going on, which is the important part. Programming is more about understanding the business logic and control flow than the syntax.

## Still didn't make sense?

I'm really determined to find a way to explain this stuff to web framework users. If you're still confused, please email me ([info@fluffyjack.com](mailto:info@fluffyjack.com)) with what you're confused about so I can try and find a better explanation so I can then help the masses.