---
title: "OS X Apps with Ruby: Part 3"
layout: post
---

## OS X Apps with Ruby: Part 3

Continuing where we left off in [part 2](http://blog.motioninmotion.tv/os-x-apps-with-ruby-part-2), we’re going to now create our really simple model object, and get the user interface to update when the volume changes.

If you’re looking for part 1, you can find it here: [Building OS X apps with RubyMotion](http://blog.motioninmotion.tv/building-os-x-apps-with-rubymotion)

### Announcement

Pre-orders have opened up for the book I’m writing called “RubyMotion for Rails Developers” which will cover iOS and OS X programming the ruby way, and will be a great introduction for both Ruby/Rails developers, and for RubyMotion beginners to mid-level developers. Pre-orders are open under a Pay What You Want pricing model, so go and check it out now at [http://book.motioninmotion.tv/](http://book.motioninmotion.tv/) and share it with your friends and followers.

### Where we left off

In the last part of this series, we hooked up our views so that they logged out the values to the terminal. We’re going to use most of the code we created in that last part to setup the controller (in this case the AppDelegate) so it updates the other UI elements.

### Being a model citizen

Before we can get our UI updating, we need a way to keep track of the volume. To do that we will create a basic model called `Track` which will just have a single `volume` attribute.

Create a directory in the `app` directory called `models`, create a file called `track.rb` in that `models` directory, and add this simple code to it so we have a basic `Track` class.

```ruby
class Track
  attr_accessor :volume
end
```

We now need a way for our controller to use the model, so we’ll add an attribute to our `AppDelegate` called `track`, then create a `Track` and set the attribute in our `applicationDidFinishLaunching:` method.

```ruby
class AppDelegate
  attr_accessor :track

  def applicationDidFinishLaunching(notification)
    self.track = Track.new

  …
```

### Sliding our values into place

Now with a way to keep track of our volume, we need to change our `takeFloatValueForVolumeFrom:` method so it saves the volume and updates the UI. Remove all the code in the method from the last tutorial, and replace it with this.

```ruby
def takeFloatValueForVolumeFrom(sender)
  self.track.volume = sender.floatValue
  self.updateUserInterface
end
```

We just called a method that doesn’t exist yet (`updateUserInterface`), time to create that now.

```ruby
def updateUserInterface
  @textField.floatValue = self.track.volume
  @slider.floatValue = self.track.volume
end
```

Run the application now and you can see that updating either the text field or the slider updates the other one. Moving on to our mute button!

### Mute the track

You might have already guessed how simple the mute method is going to be. All we’re going to do is set the track’s volume to be `0.0` and update the user interface using the method we just created.

```ruby
def mute
  self.track.volume = 0.0
  self.updateUserInterface
end
```

Then with that, we have a basic version of our app! Running the app now, you’ll be able to use all three of the controls, and they will all work together.

### Next time

So everything is working great in our simple app, but resizing the window shows we need to handle growing and shrinking our UI better. That will be the main topic of the next part!

Remember to subscribe to the RSS, follow me on [twitter](https://twitter.com/FluffyJack), or MotionInMotion on [twitter](https://twitter.com/RubyMotionTV). Share this and the last post around with your friends and colleagues too so they can learn and you can build things together :)

Send me an email if you have any questions: [info@fluffyjack.com](mailto:info@fluffyjack.com).