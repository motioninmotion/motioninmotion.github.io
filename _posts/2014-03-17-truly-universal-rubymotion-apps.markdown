---
title: Truly “Universal” RubyMotion Apps
layout: post
---

Some people may not realise how easy it actually is to write RubyMotion apps that share code between iOS and OS X with a small amount of configuration and some careful thinking about where code goes.

Both iOS and OS X share many of the same APIs and design patterns, so it's quite easy to separate code into parts that work on both platforms, and that work on a specific platform. There is also expanding support from the RubyMotion gems available for both platforms as well.

## The Rakefile

The obvious starting point is getting your application configured to work on both platforms, and that of course is going to start in our `Rakefile` where all of our configuration goes.

We can't just compile one version of our app that works on both platforms, but a good solution is being able to prefix OS X rake tasks with `osx=true` to load the OS X app instead. To do that, we start at the top of our `Rakefile`, conditionally loading the OS X or iOS RubyMotion template.

```ruby
$:.unshift("/Library/RubyMotion/lib")
if ENV['osx']
  require 'motion/project/template/osx'
else
  require 'motion/project/template/ios'
end
```

Now to run our iOS app in the simulator we can run `rake` as we normally would, or to run the OS X app we can run `osx=true rake`. This is the start of the configuration, but there is more to do like loading the correct application files.

In our setup block, we're going to need to remove the default files RubyMotion has chosen for us, and instead load our own.

```ruby
Motion::Project::App.setup do |app|
  app.name = 'MyApplication'

  app.files -= Dir.glob('./app/**/*.rb')
  app.files += Dir.glob("./app/#{app.name}/**/*.rb")
  app.files += Dir.glob("./app/#{app.name}-#{app.template}/**/*.rb")
end
```

With this setup, our new `app` folder structure would be like this:

```
app
\- MyApplication
   \- models
\- MyApplication-ios
   \- controllers
   \- models
   \- views
   app_delegate.rb
\- MyApplication-osx
   \- controllers
   \- models
   \- views
   app_delegate.rb
```

Or however you structure your application folders, but you can see that we have three distinct folders, the first being for code that works on both platforms, the second for iOS specific code, and the third for OS X code.

You might define some classes is all three folders, 'monkey patching' classes in the generalised folder that contain non-platform specific code, and then filling in more of the implementation in the matching files in the other directories. I do this quite often.

Now it's not just `app.files` we need to load properly. We also need to load the correct spec files and resources.

```ruby
Motion::Project::App.setup do |app|
  app.name = 'MyApplication'

  app.files -= Dir.glob('./app/**/*.rb')
  app.files += Dir.glob("./app/#{app.name}/**/*.rb")
  app.files += Dir.glob("./app/#{app.name}-#{app.template}/**/*.rb")
  
  app.resources_dirs = ["resources/#{app.template}"]
  
  app.spec_files -= Dir.glob('./spec/**/*.rb')
  app.spec_files += Dir.glob("./spec/#{app.name}/**/*.rb")
  app.spec_files += Dir.glob("./spec/#{app.name}-#{app.template}/**/*.rb")
end
```

This needs a bit of monkey patching to work properly for the spec files though, so you will need to add this before your setup block.

```ruby
module Motion
  module Project
    class Config
      def spec_files=(spec_files)
        @spec_files = spec_files
      end
    end
  end
end
```

I'd like to thank [Colin T.A. Gray](https://twitter.com/colinta) and [Clay Allsopp](https://twitter.com/clayallsopp) for the ideas here. They use some of this in their awesome gems to support both platforms.

So now you need go and create all of those folders, and copy over any existing code you have. If you haven't already got code for both platforms though, make sure that you generate a project for the platform you haven't got and copy over  the files in it's app folder so you have your AppDelegate ready for you.

## Other configuration

It's quite simple now to conditionally change your configuration depending on the target platform. You will likely keep your version number separate between both platforms, and there is many platform specific options you will need to keep away from the other platform.

```ruby
if app.template == 'osx'
  app.version = '2.7.94'
else
  app.version = '2.9.12'
end
```

## Creating gems

I won't be going into a full guide on creating gems, but it's easy to use these same techniques in the gems your working on as well.

For example, here is some of the important files from my [motion-momentum gem](https://github.com/FluffyJack/motion-momentum).

### Rakefile

```ruby
$:.unshift("/Library/RubyMotion/lib")
if ENV['osx']
  require 'motion/project/template/osx'
else
  require 'motion/project/template/ios'
end

require 'bundler'
Bundler.require(:development)

require 'motion-momentum'

module Motion
  module Project
    class Config
      def spec_files=(spec_files)
        @spec_files = spec_files
      end
    end
  end
end

Motion::Project::App.setup do |app|
  app.name = 'MomentumTest'
  app.identifier = 'tv.motioninmotion.MomentumTest'
  app.version = Momentum::VERSION
  app.redgreen_style = :full
  app.detect_dependencies = true

  # Get correct files for iOS or OS X
  app.files -= Dir.glob('./app/**/*.rb')
  app.files += Dir.glob("./app/#{app.template}/**/*.rb")
  app.spec_files -= Dir.glob('./spec/**/*.rb')
  app.spec_files += Dir.glob('./spec/motion-momentum/**/*.rb')
  app.spec_files += Dir.glob("./spec/motion-momentum-#{app.template}/**/*.rb")
  app.resources_dirs = ["resources/#{app.template}"]
end
```

### .travis.yml

```
language: objective-c
gemfile:
  - Gemfile
script: bundle exec rake spec && osx=true bundle exec rake spec
```

### Root file - lib/motion-momentum.rb

```ruby
Motion::Project::App.setup do |app|
  app.files += Dir.glob(File.join(File.dirname(__FILE__), 'motion-momentum/**/*.rb'))
  app.files += Dir.glob(File.join(File.dirname(__FILE__), "motion-momentum-#{app.template}/**/*.rb"))
end
```

## Really Universal

This is all the basic stuff, but like I said, there is more conditional configuration and loading you might need to do, but from here on out that's easy.

I'm taking a big focus on creating gems that work on both iOS and OS X so that there is a growing amount of support for both platforms and creating truly "Universal" apps. If you have a gem, and it makes sense to, consider pushing for OS X support in your gem too.

I'll be covering topics like this further in my upcoming book, [RubyMotion for Rails Developers](http://book.motioninmotion.tv/), which you can preorder now and get part 1. It's pay what you want so any support is much appreciated and helps me spend time writing blog posts like this, making screencasts, creating gems, and helping members of the community where I can.