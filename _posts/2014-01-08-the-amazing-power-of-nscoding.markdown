---
title: The Amazing Power of NSCoding
layout: post
---

I think [NSCoding](https://developer.apple.com/library/mac/documentation/cocoa/reference/foundation/Protocols/NSCoding_Protocol/Reference/Reference.html) is one of those parts of developing an app (iOS or OS X) that is sadly not given enough attention. It so simple to implement, but has so many uses.

I won't be covering how you can use it this post, but [next Monday's screencast](https://motioninmotion.tv/) will be about how you can use it to store data, so go sign up if you haven't.

## What is NSCoding

It's pretty simple really, NSCoding is a protocol, and it just asks you to define two methods, `initWithCoder` and `encodeWithCoder`, that's it. The definitions are stupidly simple too!

The protocol is used to define how an object should be encoded and decoded so it can be written to disk, sent over the network, copied around, it's really handy!

## How to implement it

We're obviously going to look at how to do this in RubyMotion. I'm going to use the example of a todo in a todo list app.

```ruby
class Todo
  attr_accessor :title, :description, :completed, :deadline
end
```

Pretty simple, title and description will be strings, completed a bool, and deadline a date or time. So how do we encode our custom model to write it to disk?

```ruby
class Todo
  attr_accessor :title, :description, :completed, :deadline

  def encodeWithCoder(coder)
    encoder.encodeObject(self.title, forKey:'title')
    encoder.encodeObject(self.description, forKey:'description')
    encoder.encodeObject(self.deadline, forKey:'deadline')
    encoder.encodeBool(self.completed, forKey:'completed')
  end
end
```

That wasn't hard. We're just calling `encodeObject:forKey` for each of the strings and the date/time (because they're subclasses of `NSString` and `NSDate`), and `encodeBool:forKey` because... well... we're encoding a boolean.

But how do we get that data back? We implement the other method, `initWithCoder`.

```ruby
class Todo
  attr_accessor :title, :description, :completed, :deadline

  def initWithCoder(decoder)
    self.title = decoder.decodeObjectForKey('title')
    self.description = decoder.decodeObjectForKey('description')
    self.deadline = decoder.decodeObjectForKey('deadline')
    self.completed = decoder.decodeBoolForKey('completed')
  end

  def encodeWithCoder(encoder)
    encoder.encodeObject(self.title, forKey:'title')
    encoder.encodeObject(self.description, forKey:'description')
    encoder.encodeObject(self.deadline, forKey:'deadline')
    encoder.encodeBool(self.completed, forKey:'completed')
  end
end
```

Sorted! We just did the same thing but in reverse.

## Just use it

Seriously, take advantage of it, it takes 30 seconds to write the two methods for each model, not much at all, and if you're writing tests (which you better be!), it might take 2-3 minutes to drive this out BDD style. So... just use it!

You might find one day you need it and you can just use it for what you were thinking about at that very second instead of looking up this article, setting it up, and then finally using it after you're less excited about the thing you were about to do with it.

# Take it with you!

The other great thing is, it works for both iOS and OS X! Create all your models in one, write a manager for each platform (if there really is something platform dependant) that handles encoding them and writing them to disk, then you can use your models in both projects! If that's not a selling point for NSCoding, I don't know what is!