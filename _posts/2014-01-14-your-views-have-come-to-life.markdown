---
title: Your Views Have Come To Life
layout: post
---

## Explaining Cocoa and iOS/OSX Programming Part 3

The first [two](http://blog.motioninmotion.tv/a-different-kind-of-mvc) [posts](http://blog.motioninmotion.tv/erb-vs-uiview-requesting-data) gained a lot of traction and I've been getting feedback from people about what web developers, rails developers, and other web framework developers have been struggling with when they try their hand at building apps for iOS or OSX, whether it be with RubyMotion or Objective-C.

At first glance it seems like a very different world, but the majority of the skills you have gained using the frameworks you use, can be easily ported over to developing apps for the computers, phones, and tablets you use everyday.

The language and the framework may change, but the thought processes, system development knowledge, and many other of the less thought about skills that actually make us great developers are just as applicable on other platforms. We're all becoming polyglot programmers these days. Time you stop learning 1000 ways to do the same things, and start using some of those ways to do new things. Branch out, earn more, secure more interesting jobs, build the ideas you have.

## It's alive!

![it's-alive](/assets/3i2x2R3h3d1d1X3n0h2T0s1U0X1D2o26.png)

This article is going to be all about those views you're so used to being "static", coming to life. Yes you can bring your HTML to life with a little JavaScript and CSS animations, but I'm talking about views that actually have a lifecycle. Views that are objects in their own right and are fully aware of their state. They react and respond naturally to interaction with them, and the software and hardware they're running in.

It's quite easy to get views on iOS and OSX to totally reconfigure themselves, no need to write a bunch of code to do it. New data, just let them know, they'll redraw themselves with it. Need to display 15329 rows of data, don't worry about the memory of having that many elements, they'll just reuse the same 8 or 10 so quickly you wouldn't even know.

They're aware of their animations, they're surroundings, the amount of space they have, what their siblings and parents and children are doing, when they're needed, and when they're not. It's like having every "good practice" or "makes it easy" JavaScript library built into the base system and without the download cost.

## Alright, that sounds pretty cool... show me

![talking](/assets/0p2d353A162G3y1D3X2d3y3O321g1W1t.png)

As with the other posts, I'm wanting to try and get the idea mainly in your head of how things are different. These articles are focused on you developers coming from web backgrounds. It's good to understand these ideas in concept. I've got a few examples so you can see what I'm talking about though.

The first example is AutoLayout, where you define *constraints* that your views will use to give them some basic rules like "The user's image should be 20 points away from the username, and the username should be at least 100 points wide, but it should stretch and shrink to always stay 20 points from the right edge of it's parent view".

This is much more concisely expressed in code, but you can see understand that layout behaviour can be expressed like you would describe it, not by defining a bunch of conditions. You can just define these rules, and the views will work out the rest.

You may be thinking this kind of sounds like just some CSS styles, and it kind of is, but it's more like CSS was fully aware of everything happening on screen all the time, not just a few ranges of screen widths. Watch [episode 1](https://motioninmotion.tv/screencasts/1) to get a better idea, but basically it looks kind of like this.

```ruby
Motion::Layout.new do |layout|
  layout.view self.view.tableFooterView
  layout.subviews "switch" => @switch, "help" => @help
  layout.vertical "|-15-[switch]-10-[help(==switch)]-15-|"
  layout.horizontal "|-10-[switch]-10-|"
  layout.horizontal "|-10-[help]-10-|"
end
```

This was taken from the [motion-layout gem's README](https://github.com/qrush/motion-layout).

The second example is views *responding* to the system in more fine grained ways. Using things like the accelerometer, views can respond to the phone being shaken, or maybe moved around like a real object is, inspecting it from different angles.

The greatest example of this is the iPhone's home screen in iOS 7. As you tilt the phone up or down, left or right, or whatever direction, the whole interface moves slightly with it, it's subtle, but it enhances the fact you're using an object sitting in your hand that you manipulate with your fingers.

They can respond to other things to, like direct manipulation through touch events. Not just tapping, but pinching, zooming, panning, swiping, even shoving all five fingers on it if you wanted.

Or what about a view that's responding and altering it's self based on your location, maybe the weather. It could be something as simple a external interaction like pressing pause on your headphones.

This is stuff I can't just show in a quick snippet, but it's not hard, it's basic stuff you learn to do quickly and can quite easily get to learn on the job.

The third example is system related. Your view can change what it's doing and planning to do based on what the system is doing. The system might change application, get a phone call, be interrupted by a system alert on the screen, all kinds of things. The default ways it handles these things is great already, but if you want it to respond a certain way, you have control of that too.

## Mainly an idea

Once again, I'm just trying to share the idea, so when you approach iOS and OSX development, you know it's not a straight transition, but you're more prepared. The next few articles are going to cover more basic ideas similar to [understanding how MVC works](http://blog.motioninmotion.tv/a-different-kind-of-mvc).

I implore you to try your hand at learning though. There is so many resources out there to get started with, and you're not throwing away any skills, you're just learning how to apply them to something a little different, and with these articles, you'll hopefully have the right angle of approach.

## Still didn’t make sense?

I’m really determined to find a way to explain this stuff to web framework users. If you’re still confused, please email me ([info@fluffyjack.com](mailto:info@fluffyjack.com)) with what you’re confused about so I can try and find a better explanation so I can then help the masses.