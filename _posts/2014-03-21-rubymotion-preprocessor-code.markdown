---
title: RubyMotion Preprocessor Code
layout: post
---

This morning I had an interesting thought about how we could possible have C macro like functionality in RubyMotion, and I believe I've come up with a somewhat suitable solution, though it could definitely still use some work.

The basic idea is the anything between the comments `#MBEGIN` and `#MEND` will be executed before compilation is done. If a file that is in `app.files` contains these comments, then the file in `app.files` is replaced with a preprocessed version in the `tmp` directory.

Lets look at an example to explain.

## Setting up the preprocessor

To actually use the preprocessor, you need to do a few things.

First, copy and paste `preprocessor.rb` into `lib/preprocessor.rb` in your project from this gist: https://gist.github.com/FluffyJack/9678692

Then require the preprocessor in your Rakefile and call it in your setup block.

```ruby
# -*- coding: utf-8 -*-
$:.unshift("/Library/RubyMotion/lib")
require 'motion/project/template/ios'
require './lib/preprocessor'
 
begin
  require 'bundler'
  Bundler.require
rescue LoadError
end
 
Motion::Project::App.setup do |app|
  # Use `rake config' to see complete project settings.
  app.name = 'Macros'
 
  Preprocessor.process(app)
end
```

## Using the preprocessor

Lastly, start using the syntax! Here is an example where we're adding a `test` method to our `AppDelegate` and calling it in the did finish launching method, but only if we do `test=true rake`.

```ruby
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    #MBEGIN
    #if ENV['test']
    #  @contents << "self.test"
    #end
    #MEND
 
    true
  end
 
  #MBEGIN
  #if ENV['test']
  #@contents << "def test"
  #@contents << "  puts 'test'"
  #@contents << "end"
  #end
  #MEND
end
```

The code in the macro blocks is executed in the context of the processor, and so to add lines of code to the file, we just append it to the `@contents` instance variable of the `ProcessedFile`. It also means we have access to the `ENV` hash.

I thought this was pretty awesome because it means we can run ruby code to preprocess our files. This could come in handy for debugging code, or platform specific code, because the result will never end up in the compiled files unless we tell it to.

## The result

If you run just `rake`, the preprocessed file that gets created will simply look like this.

```ruby
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    true
  end
end
```

But if you run `test=true rake` then the extra code will be added.

```ruby
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    self.test
    true
  end

  def test
    puts 'test'
  end
end
```

It's actually horribly formatted, but I've cleaned it up here. Seeming it's just a temp file though, we'll never be working with these preprocessed files.

## Would you use this?

Tell me on twitter if you would use this in your project, if enough people are interested I'll create a gem. https://twitter.com/RubyMotionTV