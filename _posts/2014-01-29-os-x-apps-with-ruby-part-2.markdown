---
title: "OS X Apps with Ruby: Part 2"
layout: post
---

Lets continue on from where we left off in [Part 1](http://blog.motioninmotion.tv/building-os-x-apps-with-rubymotion), which if you didn't read that, go back and read it now.

Also, you'll need [RubyMotion](http://www.rubymotion.com/), so go get a license now if you don't have one, because you should, because it's awesome! They have a [30 day money back guarantee](http://sites.fastspring.com/hipbyte/product/rubymotion), so you can at least give it a try. You can make cool things and make more money at work or from your clients. Not sponsored, just love it.

### Announcement

Pre-orders have opened up for the book I’m writing called “RubyMotion for Rails Developers” which will cover iOS and OS X programming the ruby way, and will be a great introduction for both Ruby/Rails developers, and for RubyMotion beginners to mid-level developers. Pre-orders are open under a Pay What You Want pricing model, so go and check it out now at [http://book.motioninmotion.tv/](http://book.motioninmotion.tv/) and share it with your friends and followers.

## Where we left off

In the part 1 we got the UI all on screen, and things setup and looking nice. Yay!

![Screen Shot 2014-01-29 at 5,10,04 pm](/assets/151J3b131T3H1G2i3O291j2p0g2I0r2k.png)

The next part will be setting things up when the user does something the app takes `action`.

## Getting connected

We're going to do this with target/action. Which if you've done iOS development, but not OS X development, is a little different.

Instead of `@button.addTarget(target, action:action, forControlEvents:events)` you just assign the target and action as properties. Like so:

```ruby
@button.target = self
@button.action = 'buttonPressed:'
```

Make sure to get that action name precise, if it accepts a parameter, have that colon, if not, remove it.

Anyway, so we're going to add this code to our three controls.

Make the changes to your code so you have the `@control.target = self` and `@control.action = 'someAction'` lines for each control. I marked each line with a comment saying `# THIS LINE` for the ones you need to add.

```ruby
@textField = NSTextField.alloc.initWithFrame([[90, 318], [100, 22]])
@textField.stringValue = '5'
@textField.alignment = NSCenterTextAlignment
@textField.bezelStyle = NSTextFieldRoundedBezel
@textField.target = self # THIS LINE
@textField.action = 'takeFloatValueForVolumeFrom:' # THIS LINE
@mainWindow.contentView.addSubview(@textField)

@slider = NSSlider.alloc.initWithFrame([[129, 62], [22, 236]])
@slider.minValue = 0
@slider.maxValue = 11
@slider.intValue = 5
@slider.target = self # THIS LINE
@slider.action = 'takeFloatValueForVolumeFrom:' # THIS LINE
@mainWindow.contentView.addSubview(@slider)

@button = NSButton.alloc.initWithFrame([[90, 20], [100, 22]])
@button.title = 'Mute'
@button.bezelStyle = NSTexturedRoundedBezelStyle
@button.target = self # THIS LINE
@button.action = 'mute' # THIS LINE
@mainWindow.contentView.addSubview(@button)
```

## Actions and reactions

So now we need to actually create those actions. There is only two. `mute` and `takeFloatValueForVolumeFrom:`. Simple stuff.

Add this to you app delegate.

```ruby
def mute
end

def takeFloatValueForVolumeFrom(sender)
end
```

Standard stuff. **Take note**: in the action assignment for the text field and slider, the string had the `:` in it, and the mute button's one didn't. This is because the mute button's action doesn't have a parameter, while the text field and slider do.

## All talk, no action

For now we're just going to log out the data we're getting.

The mute method is simple.

```ruby
def mute
  NSLog('Mute was pressed')
end
```

Then for the text field and slider's action, we're going to work out who sent it, then log that out, with the float value too.

```ruby
def takeFloatValueForVolumeFrom(sender)
  senderName = if sender == @textField
    "text field"
  else
    "slider"
  end
  NSLog("%@ sent takeFloatValueForValueFrom: with value %@", senderName, sender.floatValue)
end
```

So you can see the nice thing is getting the float value from both the text field and the slider is done the same way, which is handy. We'll expand on this code in the next part, just doing mini articles for now, so you can go and do this and have a play yourself. Go give this a try now, it's hardly any code, just write it in or copy and paste it, and play around with it a little.

## What next?

We've got everything hooked up now, we can respond to the user doing stuff, all we have to do is make it do those things, keep track of things in a model, and have the rest of the interface respond when the model is updated. The model is basically only the volume.

Remember to subscribe to the RSS, follow me on [twitter](https://twitter.com/FluffyJack), or [MotionInMotion on twitter](https://twitter.com/RubyMotionTV). Share this and the last post around with your friends and colleagues too so they can learn and you can build things together :)

Send me an email if you have any questions: [info@fluffyjack.com](mailto:info@fluffyjack.com).