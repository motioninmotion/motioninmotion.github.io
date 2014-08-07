---
title: RubyMotion The “Rails” Way
layout: post
---

This is a current proposal that I'd like your feedback on for how we can clean up how we write RubyMotion applications. It's purely conceptual at the moment, and there would have to be some framework development to make this work the way described here.

Please submit your feedback, as a joint effort we can clean up our RubyMotion applications and make it easier for Rails developers to expand their skills into RubyMotion development.

## Goals

The main points of this are:

1. Find a RubyMotion project structure where parts of the projects can be related to similar Rails parts
2. Build a framework to support this that works for both iOS and OS X
3. Take advantage of helper libraries where possible, likely RMQ, CDQ, and/or BubbleWrap
4. Come up with new conceptual design patterns if need be
5. Abstract some of the differences between iOS and OS X applications

## Concepts

### Controllers

The difference between controllers in Rails where they handle mutliple different actions versues in iOS and OS X where controllers are focused solely on one screen/window/set of views can trip developers up a lot. I don't suggest a move to the Rails way of doing things completely, but maybe we can really focus the controller's responsibilities on bringing in model data and getting the view ready for display.

```ruby
class MainViewController < RMRailsController::Base
  view MainView
  delegate MainDelegate
  stylesheet MainStylesheet
  
  def setup
    delegate.data[:posts] = Post.all
  end
end
```

In this setup, `view` will create an instance of `MainView` in the `loadView` method and assign it to `self.view`, and style it using the stylesheet assigned with `stylesheet`. The view would then have it's delegate assigned to be an instance of the class assigned by `delegate`. The delegate would basically act like the JavaScript in a web application handling interaction, of course some helpers would need to be provided for being able to access to controllers things happen in [so that it doesn't have to hold references to it](https://github.com/FluffyJack/RubyMotionOrganized/issues/1).

In the setup method would go the kinds of things you would find in `index`, `show`, or any of the other Rails controller method defintions, though data would have to be assigned to the delegate so that it has access to it.

This setup reflects basic Rails app structures:

- Controller = Controller
- View = View (already well defined)
- Stylesheet = Stylesheet (already well defined)
- Delegate = Scripts
- Model = Model (already well defined)

### Delegates

```ruby
class MainDelegate < RMRailsDelegate::Base
  def myView(myView, viewForSomePostition:position)
    SectionView.style do |v|
      v.title = self.data[:posts][position].title
      v.content = self.data[:posts][position].content
    end
  end
  
  def myView(myView, sectionSelectedAtPosition:position)
    navigate_to(DetailViewController, post: self.data[:posts][position])
  end
end
```

## Further Ideas

This is just early days for this so far, I'd love to hear your ideas. Currently the proposed solution would likely only handle VERY simple applications, and would likely have trouble working well on OS X too. Please share your thoughts. To comment and share your thoughts, go to [the living gist on GitHub](https://gist.github.com/FluffyJack/9409243).