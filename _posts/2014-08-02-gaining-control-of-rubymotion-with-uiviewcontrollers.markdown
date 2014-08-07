---
title: Gaining Control of RubyMotion with UIViewControllers
layout: post
---

In the [previous tutorial](http://blog.motioninmotion.tv/starting-a-rubymotion-application), you learnt some of the basics about RubyMotion, like how to get something on screen, the basics of working with the REPL, and how RubyMotion method names are different from other Ruby implementations.

When we did this, we did everything in the `AppDelegate` class. If we continued to do this for an entire application, things would get messy very quickly. So in this tutorial I’ll introduce you to the basics of `UIViewController`s, the C in MVC for iOS development with RubyMotion.

### Core Frameworks

Before we start though, I want to quickly explain an important concept of iOS development, the frameworks we work with.

There is many frameworks that make up the Cocoa Touch frameworks, but two that you’ll be working with (and have already worked with) are Foundation, and UIKit.

### The Foundation Framework

Foundation is well named for it’s purpose, because it’s the foundation of what we build everything on. It contains classes like `NSString`, `NSDate`, `NSArray`, etc. The core classes for working with any kind of data.

You’ve already been working with classes from this framework, because RubyMotion has a fantastic abstraction in it that gives us these classes as the Ruby versions, but with all the same functionality of Apple’s ones. They work interchangeably.

In RubyMotion, a Foundation `NSString` is a Ruby `String`, and a Ruby `String` is a Foundation `NSString`. Same for `Array`s, `Hash`es (which relate to `NSDictionary`), and more.

*Side note: The “NS” stands for NeXTSTEP, the predecessor of the operating system you’re working on right now.*

This is great because it gives us not only our normal Ruby methods, but also all of the methods from the Foundation classes too.

### The UIKit Framework

UIKit is a large collection of classes related to displaying things on the screen, such as `UIView`, `UIWindow`, `UILabel`, and `UIViewController`.

You’ll be working with classes from this framework almost constantly. We use it to display things, handle interaction and input, and much more.

Apple suggests constructing your applications using an MVC paradigm, though many other types of classes appear over time too. UIKit helps provide the classes we need to structure our applications properly. It gives us everything we need for the View and Controller parts of MVC.

### Everything is Ruby

If you’re coming from a web background, you’ll quickly realise that there isn’t something like HTML or CSS in iOS development. Instead everything is Ruby!

I suggest you embrace it, it’s quite a powerful idea. Everything in our application is a stateful object, and our views instead of being a static thing which are rendered far away from our Ruby code on a computer we have no control over, they’re an object just like anything else in our Ruby code, that we react to and interact with in real time.

### Cleaning up our code

With all of that out of the way, it’s time to clean up our code by introducing a controller into the mix.

You may have noticed in the REPL prompt when you start your application, it says something like:

```
Application windows are expected to have a root view controller at the end of application launch
```

This is iOS telling us that we’ve got some organising to do, to keep our code in the right place.

When an application finishes launching, it expects two things to be in place. First is that there should be a `UIWindow` set up and that it’s been made to be the `key` window. The second thing is that `UIWindow` should have a property set on it called `rootViewController`.

The root view controller of a window is the controller that will be used for this window at the start. This can change over the lifetime of the application, and we have ways of navigating between controllers, but for now, we’re just going to have one.

As the name of `UIViewController` implies, they are controllers of views. When the application is focusing on a specific view controller, it’s views will be added to the window.

### Moving the code around

In your `app_delegate.rb` file, delete all the lines related to creating the `UILabel` and adding it to the window. Then we’re going to set the `rootViewController` property of our `UIWindow`.

```ruby
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    @window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)
    @window.rootViewController = MainController.new
    @window.makeKeyAndVisible

    true
  end
end
```

We’re setting the `rootViewController` property to be a new instance of a `MainController` class that we’re going to create in a second.

When the application launches now (it will crash if you try to launch it right now because `MainController` doesn’t exist) it will use `MainController` to set up the view.

### Creating our MainController

All controllers inherit from `UIViewController`, or a speciality subclass of it. So we’re going to create a new class that does that now. In your app directory, create a new file called `main_controller.rb`, and add the following to it.

```ruby
class MainController < UIViewController
  def viewDidLoad

    label = UILabel.new
    label.text = "Hello World"
    label.textColor = UIColor.whiteColor
    label.frame = [[20, 50], [280, 50]]

    view.addSubview(label)

  end
end
```

The body of the `viewDidLoad` method is almost exactly the same as the code we had in our `AppDelegate`, except for one important difference, we’re adding the `label` to the `view` property of our controller, instead of to the window directly. This parent view is created for us when the controller is getting set up.

Like the plan was, our `MainController` inherits from `UIViewController`, which contains a large amount of functionality related to display things on screen and handling interaction.

The `viewDidLoad` method that we defined is one of the standard lifecycle methods that will be called by the application as things happen. There is many others we’ll cover over time such as `viewDidAppear:animated:` and `loadView` as well.

### Getting interactive

Our application is pretty boring at the moment, it just sits there and says hello. Lets at least add some basic interaction.

Underneath where we create and set up our label, we’re going to create a button using the `UIButton` class from UIKit. This is a kind of `UIControl` that handles interaction.

```ruby
class MainController < UIViewController
  def viewDidLoad

    label = UILabel.new
    label.text = "Hello World"
    label.textColor = UIColor.whiteColor
    label.frame = [[20, 50], [280, 50]]

    view.addSubview(label)

    button = UIButton.new
    button.setTitle("Press Me", forState: UIControlStateNormal)
    button.setTitle("Don't Press Me", forState: UIControlStateDisabled)
    button.frame = [[20, 100], [280, 50]]

    view.addSubview(button)

  end
end
```

If you run the application at the moment, this button doesn’t actually do anything, but there is already some code here for us to talk about, specifically the setting of the text on the button.

As an interactive view on the screen, a `UIButton` could be in multiple states, such as the normal/default state when it’s just doing it’s thing, or maybe the disabled state where all interaction is disabled.

By setting attributes like the title for the normal state, this becomes the default for all other states as well.

We’ve set a title for the disabled state as well though, so use hold down the Command key on your keyboard and select the button in your REPL, and then disable the button using this:

```ruby
self.enabled = false
```

When you hit enter, you’ll see the text on the button change from “Press Me” to “Don’t Press Me” because we’ve gone from the normal state, to a disabled state.

Exit the app and REPL and lets continue.

### Target/Action

When working with things like buttons in iOS, one of the ways we react to events is by using Target/Action. You can imagine this as when an event, such as being tapped happens, the button will shoot an arrow towards a target (usually our controller), and when that arrow hits, it will set off an action to occur (usually a method on our controller).

Lets set up our own Target/Action now on our button to toggle the text on our label.

```ruby
class MainController < UIViewController
  def viewDidLoad

    @label = UILabel.new
    @label.text = "Hello World"
    @label.textColor = UIColor.whiteColor
    @label.frame = [[20, 50], [280, 50]]

    view.addSubview(@label)

    button = UIButton.new
    button.setTitle("Press Me", forState: UIControlStateNormal)
    button.setTitle("Don't Press Me", forState: UIControlStateDisabled)
    button.addTarget(self, action: 'button_pressed:', forControlEvents: UIControlEventTouchUpInside)
    button.frame = [[20, 100], [280, 50]]

    view.addSubview(button)

  end

  def button_pressed(sender)
    new_text = if @label.text == "Hello World"
                 "Goodbye Friend"
               else
                 "Hello World"
               end
    @label.text = new_text
  end
end
```

The first thing we need to do is change our `label` variable from being a local variable to an instance variable, because we’re going to be accessing it in another method.

Second we’re adding this line of code:

```ruby
button.addTarget(self, action: 'button_pressed:', forControlEvents: UIControlEventTouchUpInside)
```

If we read through this line “in english”, then we can read it as: add a target of `self` (our controller) that will have the `button_pressed:` (pay attention to the colon) method called on it, when the control (our button) has a finger lifted while it’s still on the button.

The reason we want this to happen on “touch up inside” instead of something like “tap”, is that it allows a user to cancel an action by dragging their finger off the button before they lift it, incase they tap it accidentally.

Now to explain that colon on the end of the action name, by looking at our new method `button_pressed:`.

The `button_pressed:` method takes an argument called `sender`, which is the button. The colon on the end of the method name says that the method we’re going to call has a parameter after this part of the method name.

If we only had `button_pressed`, without the colon, then that would be an entirely different method.

The code inside our `button_pressed:` method is fairly simple, we’re just checking if the current `text` on our label is “Hello World”, and if so setting it to be “Goodbye Friend”, otherwise we’re setting it to be “Hello World”, so when you run this now, you’ll get a toggling effect when you press the button.

### Cleaning up our view code

It’s generally not good practice to include all of this view creation code in the controller. The controller should be in charge of controlling the view, and not creating all the bits of it.

So what we’re going to do is move all of our view set up code into our own `UIView` subclass. Before we do that though, we need to tell our controller how to load it by defining the `loadView` method. Add this to the top of your `MainController` class.

```ruby
def loadView
  self.view = MainView.new
end
```

The `loadView` method gets called when things are getting ready to be displayed on the screen. Previously we were just using the default functionality provided by inheriting from `UIViewController`. Now we’re creating our root view for our controller, and assigning it ourselves.

*Note that we’re using `self.view =` instead of just `view =` because in the second case we would be setting a local variable.*

Now we have to delete all that view creation code from our `viewDidLoad` method **except for** the the line that sets up the target-action. We’re also going to be accessing our button through our view, instead of as a local variable.

```ruby
def viewDidLoad
  view.button.addTarget(self, action: 'button_pressed:', forControlEvents: UIControlEventTouchUpInside)
end
```

The reason we do this in `viewDidLoad` instead of `loadView` is that this is the appropriate place in the lifecycle of the view and it’s controller to start getting interaction ready.

The `button_pressed:` method needs some small changes too, to stop using an instance variable, and instead access the label through the view.

```ruby
def button_pressed(sender)
  new_text = if view.label.text == "Hello World"
               "Goodbye Friend"
             else
               "Hello World"
             end
  view.label.text = new_text
end
```

That’s all the changes we need in our controller, so lets move on to creating our `MainView` class.

### Creating the MainView

Start out by creating a new file in the `app` directory called `main_view.rb`. In here we’re going to create our `MainView` class which will inherit from another UIKit class called `UIView`, which is also the superclass for our `UILabel`, and in the ancestry chain of our `UIButton`.

Then we’ll move all of our old view code into here and make some small changes such as making the views accessible via attribute readers on our class.

```ruby
class MainView < UIView
  attr_reader :label, :button

  def init
    super

    @label = UILabel.new
    @label.text = "Hello World"
    @label.textColor = UIColor.whiteColor
    @label.frame = [[20, 50], [280, 50]]

    addSubview(@label)

    @button = UIButton.new
    @button.setTitle("Press Me", forState: UIControlStateNormal)
    @button.setTitle("Don't Press Me", forState: UIControlStateDisabled)
    @button.frame = [[20, 100], [280, 50]]

    addSubview(@button)

    self
  end
end
```

The notable changes in our existing code that we moved over are that we’re using an instance variable for both our label and button now, so that our `attr_reader`s at the top of this class work, and that we were about to remove calling `addSubview` on anything in particular, instead seeming it’s just a method on the view, we can just call it on it’s own in both places. Other than that, nothing really has changed.

We’ve got a new topic though for RubyMotion development, the two different kinds of initialisers that exist when we’re defining them. I’ve covered usage, but not definition.

Before I explain, I’ll point out that using `new` to create an instance of a class is just an shortcut for `alloc.init` when it comes to classes that inherit or are from the Cocoa Touch frameworks like Foundation or UIKit.

### Two different kinds of initializers

When you’re creating a plain Ruby class that **does not** inherit from one of these Cocoa Touch framework classes, you can still define `initialize` like you would in normal Ruby implementations.

```ruby
class Person
  attr_reader :name, :email

  def initialize(name, email)
		@name, @email = name, email
  end
end
```

There is a big difference for classes that inherit from Cocoa Touch classes though (so basically that aren’t plain old ruby objects). In this case you can use `initialize`, instead `alloc` and then a specialised initializer needs to be called, even if that initializer is just init like we’re doing. Like I said too, new is just a shortcut for calling `alloc.init`.

The other difference with these Cocoa Touch initializers is that you should call `super` at the start, and return `self` at the end. This is just the way the Cocoa Touch classes work.

### We’re clean!

With all of this done, we now have a basic working application that is well structured! We’ve also once again learnt an enormous amount within this tutorial. You’re well on your way now to being able to create real apps, with some of the core concepts in your bag of tricks.

Remember to keep an eye out for further tutorials, I’ll be posting as much as possible.

Last few things are that you can learn more and support me by subscribing the [MotionInMotion](https://motioninmotion.tv/) weekly RubyMotion screencast, which is only $9AUD/month, or by purchasing my upcoming book, [RubyMotion for Rails Developers](http://book.motioninmotion.tv/), which will be covering these topics in more depth, and taking it much further than this. Part 1 is already out explaining a lot of the core concepts of iOS and OS X development in great depth.

Feel free to email me too, my email address is [info@fluffyjack.com](mailto:info@fluffyjack.com).