---
title: How to Test RubyMotion with any Ruby Test Framework
layout: post
---

You may have been working with RubyMotion for a while, maybe you've only heard about it, but something most people are aware of is that the main test framework is MacBacon.

This isn't a post attacking MacBacon, because it's a tool that does an absolutely amazing job for what it's built for. The problem with testing using MacBacon is that you have to wait for your app to compile, which is fast, but not always fast enough for a good feedback cycle when you're doing TDD.

I'm a big fan of outside-in TDD, and though I try hard to test my RubyMotion projects, sometimes I get angry about the feedback cycle not being as fast as the sub-second testing I'm used to, which leads me to skip something.

Whenever I look back at code I've written after skipping the testing, I can see it being hard to change, it has a poor design, and eventually when I have to change it I hit myself for creating it without tests because things just start breaking.

## How I fixed this

The previous blog post I wrote about ["Writing Gems for Ruby and RubyMotion"](http://blog.motioninmotion.tv/gems-for-ruby-or-rubymotion) provides the beginning of a solution to this.

The next step is realising that gems don't have to be shared between projects, we can have gems that are stored within a project and are just used to keep some code together.

What this realisation lead me to do is simply create a gem inside my lib directory called something like `appname_use_cases`, `appname_core`, or maybe a more specific name if I want to have multiple smaller gems that I might eventually share with another project.

This is really simple to do, start of course by creating a folder in your `lib` directory for your gem. Now we need to create a gemspec.

**mim_core.gemspec**

```ruby
Gem::Specification.new do |s|
  s.name        = 'mim_core'
  s.version     = '0.1.0'
  s.licenses    = ['MIT']
  s.summary     = "Project gem for plain ruby stuff"
  s.description = "This gem allows us to test core business logic and application code without the UI, DB, or API and then have those plugged in when we use this in the RubyMotion app"
  s.authors     = ["Jack Watson-Hamblin"]
  s.email       = 'info@fluffyjack.com'
  s.files       = Dir.glob('lib/**/*.rb')
  s.homepage    = 'http://motioninmotion.tv'
  s.require_paths = ["lib"]
end
```

That was simple enough, now we can create a special file that will add our gem's files to our RubyMotion project.

**gem_dir/lib/rubymotion.rb**

```ruby
# encoding: utf-8

unless defined?(Motion::Project::Config)
  raise "This file must be required within a RubyMotion project Rakefile."
end

lib_dir_path = File.dirname(File.expand_path(__FILE__))
Motion::Project::App.setup do |app|
  app.files.unshift(Dir.glob(File.join(lib_dir_path, "mim/**/*.rb")))
end
```

Finally we just need to include our gem in our projects Gemfile.

**Gemfile**

```ruby
source 'https://rubygems.org'

gem 'rake'
gem 'mim_core', path: 'lib/mim_core', require: 'rubymotion'
```

Now we have a shell of a gem that our project can use, but here comes the fun part. It's just a gem, one that we can test with any test framework we want!

## Choose your Test Framework

From here on out you can test drive your gem as much as you like. Anything that doesn't depend on Apple frameworks or RubyMotion-only gems, you can now test drive in this gem folder instead of in your normal app folder.

So create a `test` or `spec` folder, write some specs, and write some code in `lib/mim` to make it pass! Woo!

You can now use minitest/testunit, rspec, bacon, cucumber, whatever you want! There is no waiting for your code to compile before you tests run.

## UI, DB, and APIs

Of course this does nothing for the code that depends on Apple's frameworks or a RubyMotion-only gem like AFMotion or CDQ.

I'll leave it up to you to decide whether you will test drive those parts. I suggest you look into [DDD and Hexagonal Architectures](http://confreaks.com/videos/3316-railsconf-domain-driven-design-and-hexagonal-architecture-with-rails) and [Entity, Boundary, Interactor](http://confreaks.com/videos/759-rubymidwest2011-keynote-architecture-the-lost-years). This way you can just plug in your UI, DB, and APIs into your core plain ruby code.

