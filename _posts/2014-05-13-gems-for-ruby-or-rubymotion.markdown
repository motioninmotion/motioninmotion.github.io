---
title: Writing Gems for Ruby and RubyMotion
layout: post
---

Back in March, I wrote about [Truly "Universal" RubyMotion Apps](http://blog.motioninmotion.tv/truly-universal-rubymotion-apps) where I presented a way for you to keep the source code for your iOS and OS X applications in one project directory so that they can share some code like your models.

This was really well received, but I wanted to take this further. I've been looking into [DDD and Hexagonal Architectures](http://confreaks.com/videos/3316-railsconf-domain-driven-design-and-hexagonal-architecture-with-rails) and [Entity, Boundary, Interactor](http://confreaks.com/videos/759-rubymidwest2011-keynote-architecture-the-lost-years), which made me think about how we could actually share the core parts of our applications (web included) between all platforms and then just plug it into our MVC.

I've worked out a fairly simple way to make your gems work for normal Ruby projects and RubyMotion projects. This is only a starting point, of course there is more work to be done in larger codebases, but this could let you test your core functionality separate from your delivery platforms so that you know your business logic works.

Lets dive in.

## The “Timestamps” gem

This isn't a real gem, I've just created it as a very basic example. The core of the gem is very simple, it just converts "12:45:32" into "12h 45m 32s". It is horrible code because I just wanted to get something going.

**lib/timestamps/base.rb**

```ruby
module Timestamps
  class Converter
    def initialize(timestamp_string)
      @timestamp_string = timestamp_string
    end

    def convert_to_hours_minutes_seconds
      parts = @timestamp_string.split(':')
      seconds = parts.pop || 0
      minutes = parts.pop || 0
      hours   = parts.pop || 0
      "#{hours}h #{minutes}m #{seconds}s"
    end
  end
end
```

We use it by creating an instance of `Timestamps::Converter` passing the string to convert in the constructor, and then call `convert_to_hours_minutes_seconds` to get the new string.

```ruby
converter = Timestamps::Converter.new('3:23:32')
hms = converter.convert_to_hours_minutes_seconds
#=> "3h 23m 32s"
```

## Creating Connections

The next thing we need is a way to get ahold of this class. In Ruby we just want to `require 'timestamps'` and have it ready to go, while in RubyMotion we need to add it to the list of files to be compiled.

**lib/timestamps.rb**

```ruby
require 'timestamps/version'
require 'timestamps/base'
```

**lib/timestamps-rubymotion.rb**

```ruby
# encoding: utf-8

unless defined?(Motion::Project::Config)
  raise "This file must be required within a RubyMotion project Rakefile."
end

lib_dir_path = File.dirname(File.expand_path(__FILE__))
Motion::Project::App.setup do |app|
  app.files.unshift(Dir.glob(File.join(lib_dir_path, "rubymotion/**/*.rb")))
  app.files.unshift(Dir.glob(File.join(lib_dir_path, "timestamps/**/*.rb")))
end
```

That was pretty easy. You can see that we have a special directory just for our RubyMotion related stuff too. In that directory we can monkey patch classes that differs from MRI as well.

## Dealing with require

Right now we don't use `require` anywhere that will be compiled by RubyMotion, but so that when we're working on things in the future we don't have to go back and fix things like this, lets put in a simple fix for removing the error case of using `require` in RubyMotion with just an empty definition.

**lib/rubymotion/require-fix.rb**

```ruby
def require(*args); end
```

## Using the gem

In any standard Ruby project, you can now include this gem in your Gemfile using `gem 'timestamps'`, but now we have the option of using it in a RubyMotion app's Gemfile with:

```ruby
gem 'timestamps', require: 'timestamps-rubymotion'
```

## Converting gems

I've had very quick attempts at converting existing pure Ruby gems (because obviously gems with C-extensions won't work) to use this method, it's really not hard. The three main problems are:

* Lots of gems are for Rails or don't make sense with RubyMotion
* Gems depend on other gems a lot of the time, which means converting all of them
* Some gems use C-extensions

## Ideas

Share some of your ideas with me about this on twitter using the links below.

