---
title: Starting a RubyMotion Application
layout: post
---

Welcome to the first in a series of posts about creating a RubyMotion application, I’m not entirely sure where we’ll end up, but let me take you on a journey as we create something.

### Generating a project

RubyMotion is a toolchain, with a great many tools you can use, but the main one is the one you use to start a project, without it you won’t get much further.

I’m assuming you’ve already grabbed a copy of [RubyMotion](http://www.rubymotion.com/) and installed it on your Mac, so open up a terminal (I use [iTerm 2](http://iterm2.com/)) and lets get started.

Find a directory where you’d like to store your projects, and then run this command:

```
motion create HelloWorld
```

The `motion` command line tool is provided by RubyMotion, mainly for generating projects. Many of the other tools RubyMotion provides are used through `rake`.

We’re using the `create` command of the tool, to generate a project called “HelloWorld”.

When you run that command you should get this as the output:

```
Create HelloWorld
Create HelloWorld/.gitignore
Create HelloWorld/app/app_delegate.rb
Create HelloWorld/Gemfile
Create HelloWorld/Rakefile
Create HelloWorld/resources/Default-568h@2x.png
Create HelloWorld/spec/main_spec.rb
```

Lets walk through those files quickly.

### The files in a RubyMotion application

First we of course have our project directory, where we’ll be working with all of our project’s files, followed by a `.gitignore` file with some smart defaults in there.

Inside the `app` directory, where all of our code to be compiled will go, we have the `app_delegate.rb` file. Open that up to take a look at it.

```ruby
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    true
  end
end
```

The `AppDelegate` class is a communication gateway between the rest of our application, and the things that happen with the system, such as the application being launched, or put into the background.

### Weird method names

Currently we only have one method in there, which we’ll be editing soon. This is your first introduction to the different style of method names in RubyMotion, which look almost like keyword arguments in other Ruby implementations, but have a very important difference.

If you haven’t seen this before, you’ll see the method and think that the method name is `application`, where in fact it’s `application:didFinishLaunchingWithOptions:`.

Method names in RubyMotion are made up of the part before the parentheses, as well as any following keyword argument looking parts like `didFinishLaunchingWithOptions`.

The other parts of the line in the parentheses where we begin defining this method (`application` and `launchOptions`), are the local variable names that will be set to when the method is called.

Though we wouldn’t call this method directly, because that’s for the system to do, as an example of how a method with this method name would be called, you could use this method like so:

```ruby
@app_delegate.application(@myapp, didFinishLaunchingWithOptions: {option_one: 'foobar'})
```

This has an interesting (and intentional) side effect, which is that we can define a method “multiple times”. For example, these would all be different methods.

```ruby
def exportData(data, toFile: file)
def exportData(data, toURL: url)
def exportData(data, toDatabase: database)
```

Though in other Ruby implementations, these would overwrite each other, in RubyMotion, we can overload these methods. You can still use normal Ruby syntax for defining methods, in fact it’s very common to see RubyMotion developers doing that.

Understanding that method overloading exists in RubyMotion is important though, because you will have to work with overloaded methods regularly while using the iOS frameworks (Cocoa Touch).

### Back to the files

Getting back to looking at all of the files the `motion` tool generated for us, we can see a `Gemfile` next in our list. Good news is that this `Gemfile` works just like your normal Ruby `Gemfile`s, in fact it is a normal `Gemfile`.

Something to note though is that most normal Ruby gems don’t work with RubyMotion (as well as most not making sense to be used in an iOS app anyway).

### RubyGems in RubyMotion

There is nothing particularly different about gems you use with RubyMotion, in fact you can technically use any gem.

The difference is that when gems are required, they’re used in the build process, and not included in your actual application.

For a gem to provide functionality for a RubyMotion application, it has to add the files it wants included, into the list of files to be compiled.

Within a RubyMotion application’s code, there is no concept of “requiring” a file. Instead all files that contain code that might be needed are added to a list, and all of those files are compiled, meaning the classes defined in any file, are available in all files that get compiled.

There is a website dedicated to the RubyMotion specific gems called [motion-toolbox.com](http://motion-toolbox.com/).

### The Rakefile

Within a RubyMotion project, the `Rakefile` is extremely important. It’s the commander of our build processes and other tools, and where we store all of our configuration.

Take a look at the `Rakefile` we have currently.

```ruby
$:.unshift("/Library/RubyMotion/lib")
require 'motion/project/template/ios'

begin
  require 'bundler'
  Bundler.require
rescue LoadError
end

Motion::Project::App.setup do |app|
  # Use `rake config' to see complete project settings.
  app.name = 'HelloWorld'
end
```

The first two lines are just including all the RubyMotion tools for iOS projects, and that `begin rescue` block of code is just your standard bundler loading.

The interesting part is the `Motion::Project::App.setup` block. Looking in it you can see it’s setting the name of our application.

Try running `rake config`, and you’ll see a bunch of default settings pop up on screen which you can use in this setup block. We only need the name to be set at the moment, so we won’t change anything here.

### The resources directory

Next in our adventure through this directory structure is the `resources` directory which currently only contains a plain black image.

This image is our launch image, and you can add a great many variations for the different screen sizes and resolutions.

The resources directory isn’t just for this image though. This is where you’ll put all of your static assets like images, fonts, videos, etc.

During the build process, everything in this directory will be copied into the application’s bundle to be installed on the device or simulator so your application can use it.

### Specky Bacon

The last file in our journey through this file tree is the `main_spec.rb` file.

RubyMotion comes with a RSpec like tool called [MacBacon](https://github.com/alloy/MacBacon), which you can use to create unit, acceptance, and integration tests. Though this is a topic for another time.

### First run

Now that you know a little about all the files you’ve got at hand, you’ll be glad to know that this is a “functioning” iOS application.

To build and run your application, simply run `rake` from your project’s directory. This will compile the application, and load the iPhone simulator, installing your application onto it.

Once your application loads, you’ll be given a REPL prompt in the terminal that we’ll play with soon. To get back to your terminal prompt, simply type `exit` and the application will exit and control will go back to your normal terminal prompt.

The application is not very exciting right now, it’s just a black screen, but before we move on, I’ll show you how to load your application into the iPhone 4 sized (3.5in) simulator, which is simply running `rake`, but with `retina=3.5` on the end. So run `rake retina=3.5` to get a 3.5 inch simulator instead of the 4 inch.

### Time to say Hello

Now that we’ve got all of that covered, lets get the words “Hello World” on our screens.

In your editor, open up the `app_delegate.rb` file we were looking at before. We’re going to change this to get something on our screen. Change the file to look like this.

```ruby
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    @window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)

    label = UILabel.new
    label.text = "Hello World"
    label.textColor = UIColor.whiteColor
    label.frame = [[20, 50], [280, 50]]

    @window.addSubview(label)

    @window.makeKeyAndVisible

    true
  end
end
```

Before we look over these changes, run your application (using `rake`) so you can see what we did, but just incase you’re only reading along, here’s a picture.

![Screen Shot 2014-08-01 at 3,41,49 pm](/assets/0K1H3q1o0C2b3y3H2p1f2a090G440C2J.png)

With your application running, hold down the Command key on your keyboard, and move your mouse around the simulator’s screen. You’ll see a red box around the edge of the screen, and when you hover over the text, you’ll see a red box around the text instead.

This is a tool that’s used in conduction with RubyMotion’s amazing REPL. Pay attention to the REPL prompt as you mouse around the simulator screen, and you’ll see it’s changing. This is it selecting the element you’re hovering over.

Hold down Command and hover over the text, then let go, and you should have something like `(#<UILabel:0x8c03aa0>)>` as your REPL prompt.

In the REPL, write this and then look at what it did in the simulator.

```ruby
self.text = “Goodbye Friend”
```

You’ll see now that we changed the text on the fly using the REPL. RubyMotion’s REPL is almost a combination of the Developer Tools in your browser, with the Rails console, because everything is in our application, and we can access everything from this REPL.

Lets try another one. Select the whole screen (the red border should be around the entire screen) so that you get a prompt like `(#<UIWindow:0x8e0f730>)>`, then put this into your REPL.

```ruby
self.backgroundColor = UIColor.blueColor
```

With that, the background of our entire application is now blue! The `UIColor` class is a special class used in iOS applications to define a color. You’ll see much more of it over time.

### Back to that code

Time to exit your application, because we never reviewed what all that code we added to our `app_delegate.rb` file was.

Here it is again, we’re going to look over it line by line.

```ruby
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    @window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)

    label = UILabel.new
    label.text = "Hello World"
    label.textColor = UIColor.whiteColor
    label.frame = [[20, 50], [280, 50]]

    @window.addSubview(label)

    @window.makeKeyAndVisible

    true
  end
end
```

We’re only looking at the parts inside the method definition, so we’ll start with the line that defines `@window`.

```ruby
@window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)
```

A `UIWindow` is the bottom layer of the “views” that will be displayed on your screen, every other view will be drawn on top of this. A view is simply a subclass of `UIView`, which has many speciality subclasses, such as `UILabel` which we’ll look at next.

The `alloc.initWithFrame` part of this may have you tilting your head trying to work it out. This is the way Apple's frameworks allocate (`alloc`) and initialize (`initWithFrame`) an instance of a class. Many classes in Cocoa Touch and in other iOS frameworks, including those from third-parties, have multiple initializers dedicated to taking different kinds of parameters.

Initializers will start with either just `init` or `initWith`, and in this case, we have one that takes the frame (which I’ll cover in a second). The part in the parentheses is just saying the frame should be the size of the screen.

```ruby
label = UILabel.new
label.text = "Hello World"
label.textColor = UIColor.whiteColor
label.frame = [[20, 50], [280, 50]]
```

A `UILabel` is a view that has the job of displaying text. It has a bunch of methods on it for setting up how that text should be displayed, two of those is being able to set the `text` itself, which is the second line of this block, and another is the `textColor`, which we set using the `UIColor` class to give us a color.

The last line of the block for the `UILabel` sets the `frame`. The `frame` is a position on the screen relative to a view’s superview (in this case, the window which takes up the whole screen).

Before a view can actually show up on screen, it needs to have somewhere to live, so we use the `addSubview` method to add it to either a `UIWindow`, or something that has already been added to the `UIWindow`.

```ruby
@window.addSubview(label)
```

Lastly, we need to tell the application that this `UIWindow` that we’ve created is the one we want as the main (key) window, and that we want that to be visible now that it’s all set up.

```ruby
@window.makeKeyAndVisible
```

The `true` at the end of the method (which was there when we generated this project) is just the return value to let the application know we did launch alright.

### Moving past HelloWorld

I’ll be continuing to write more, but there is also a great many other resources out there, including some of my own for you to continue learning with.

There is [MotionInMotion](https://motionintmotion.tv/), my screencasts, which are only $9/month, [rubymotion-tutorial.com](http://rubymotion-tutorial.com/) by Clay Alsopp, as well as some [great guides on the RubyMotion website](http://www.rubymotion.com/developer-center/).

You can also check out my upcoming book [RubyMotion for Rails Developers](http://book.motioninmotion.tv/), which as of this writing, has part 1 completed, and is up for sale under a pay what you want model, so you can pay anything from $1 up to $1000.

Look out for the next part of this series, and always feel free to email me questions to [info@fluffyjack.com](mailto:info@fluffyjack.com).