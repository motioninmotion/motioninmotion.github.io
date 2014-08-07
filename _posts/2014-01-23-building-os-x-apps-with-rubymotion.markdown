---
title: Building OS X apps with RubyMotion
layout: post
---

I'm doing my planning for the OS X episodes on [MotionInMotion](https://motioninmotion.tv/) at the moment, a bit ahead of time so I make sure I do it from the right angle. In preparation for them, I'm starting back at the beginning and trying to forget what I know temporarily while I go through a bunch of introduction stuff.


The first of which is the getting started guide ["Start Developing Mac Apps Today"](https://developer.apple.com/library/mac/referencelibrary/GettingStarted/RoadMapOSX/chapters/01_Introduction.html) from Apple's developer docs. I'm currently doing the [first tutorial](https://developer.apple.com/library/mac/referencelibrary/GettingStarted/RoadMapOSX/books/RM_YourFirstApp_Mac/Articles/Introduction.html) in there, but of course the RubyMotion way, which means doing things without Xcode and Interface Builder.

I'd also like to mention that Kickcode is releasing a book called ["Building Mac OS X apps with RubyMotion"](http://kickcode.com/building-mac-os-x-apps-with-rubymotion/) if you're interested. I'm not getting paid to share this, just excited for it!

## Follow along while I re-learn

This is going to be a series where I just post each part I did to create the TrackMix app in RubyMotion instead of Obj-C. I'm not going to do much explaining of OS X development concepts, this is more just a learn by doing and look up what you're interested in kind of deal. Let's just jump right into it.

## Creating the app

Obvious first step is of course generating our RubyMotion app. Run the following command wherever you store your RubyMotion apps.

```
motion create --template=osx TrackMix
```

This will create the foundations for our app, and if you've done OS X development in Xcode, it creates a project very similar to the basic Cocoa Application template in Xcode.

Before we move on to putting things on the screen or run `rake`, lets configure our window.

## Sizing things up

Open your `app/app_delegate.rb` file, and in the `buildWindow` method, change the window size on `line 8` to this.

```ruby
...
@mainWindow = NSWindow.alloc.initWithContentRect([[240, 180], [280, 360]],
...
```

Alrighty, you can run `rake` now and see your blank slate, then when you're done, we'll start creating the interface.

## The text field

If you didn't take a look at the links at the top of this post, we're creating a little volume slider thingy, so we need a text field to show the current value of the volume, the slider, and a 'Mute' button. We're starting with the text field.

Write this code in the `buildWindow` method under the line that says `@mainWindow.orderFrontRegardless`.

```ruby
...
@textField = NSTextField.alloc.initWithFrame([[90, 318], [100, 22]])
@textField.stringValue = '5'
@textField.alignment = NSCenterTextAlignment
@textField.bezelStyle = NSTextFieldRoundedBezel
@mainWindow.contentView.addSubview(@textField)
...
```

Easy, we've created our text field and it's sitting at the top of our screen, has nice pretty rounded corners, has the initial value of 5, and has it's text centred. Run `rake` if you like.

## The slider

Next is our swooshy slider! This one is pretty basic, we're just going to create it, add it to the screen, and set the min, max, and initial values. Just put this under the text field code, and still in the buildWindow method.

```ruby
...
@slider = NSSlider.alloc.initWithFrame([[129, 62], [22, 236]])
@slider.minValue = 0
@slider.maxValue = 11
@slider.intValue = 5
@mainWindow.contentView.addSubview(@slider)
...
```

All sorted, run `rake` now if you like.

## The mute button

Last but not least is our mute button, we'll put this under our slider (both in the code and on screen).

```ruby
...
@button = NSButton.alloc.initWithFrame([[90, 20], [100, 22]])
@button.title = 'Mute'
@button.bezelStyle = NSTexturedRoundedBezelStyle
@mainWindow.contentView.addSubview(@button)
...
```

And that's our UI done!

## Things to note

You may have noticed, the view coordinates aren't the same as when you're doing an iOS application. On iOS, the view origin starts at the top left of the screen, on OS X, it's the bottom left.

## What's next?

Well... as I go through the tutorial, I'll post more. We definitely need to get the UI to grow and shrink depending on the window size, because right now if you expand the window it just sits in the bottom left corner.

Then of course we need to get all the code hooked up so when we change the number in the text field it updates the slider, and vice versa. We need to get mute working too.

To keep updated with this series, just subscribe to my RSS, which you can find just above my author bio just below. Also follow me on twitter, either me personally by following [@FluffyJack](https://twitter.com/FluffyJack) or follow MotionInMotion by following [@RubyMotionTV](https://twitter.com/RubyMotionTV).

Got some feedback? Send me an email to [info@fluffyjack.com](mailto:info@fluffyjack.com).

Part 2 is out, [read it now](http://blog.motioninmotion.tv/os-x-apps-with-ruby-part-2).

### Announcement

Pre-orders have opened up for the book I’m writing called “RubyMotion for Rails Developers” which will cover iOS and OS X programming the ruby way, and will be a great introduction for both Ruby/Rails developers, and for RubyMotion beginners to mid-level developers. Pre-orders are open under a Pay What You Want pricing model, so go and check it out now at [http://book.motioninmotion.tv/](http://book.motioninmotion.tv/) and share it with your friends and followers.