---
title: Grand Central Dispatch Isn't Just For Background Queues
layout: post
---

Tonight at [SydInMotion](http://sydinmotion.com/) I got in a discussion that lead to me explain `Dispatch.once` and why it's so handy. I've decided to write this up as a quick tip as it's useful in almost any application.

## Global Managers

The first case that you might want to use `Dispatch.once` is with a singleton object. During the lifetime of the application, you only ever want this object to be set up and created once, and you need to be able to access it at all times.

One example might be a `NavigationManager` that gives you a central space to move around your navigation stack when you're using a `UINavigationController`.

```ruby
class NavigationManager

  #######################
  # IMPORTANT BIT START #
  #######################

  class << self
    def shared
      # create the manager only once, ever
      Dispatch.once { @shared = new }

      # then return it outside of the once block
      @shared
    end
  end

  #####################
  # IMPORTANT BIT END #
  #####################

  attr_reader :navigation_controller

  def initalize
    @navigation_controller = UINavigationController.new
    start_current_workflow
  end

  def navigate_to(vc)
    @navigation_controller.pushViewController(vc, animated: true)
  end

  def pop
    @navigation_controller.popViewControllerAnimated(true)
  end

  def go_back_to(controller_class)
    navigation_controller.popToViewController(controller_for_class(controller_class), animated: true)
  end

  private

  def start_current_workflow
    # this could be refactored with
    # replace conditional with
    # polymorphism
    if SessionManager.current_user.state == :missing
      navigate_to(MissingWorkflow.controller)
    else
      navigate_to(DashboardController.new)
    end
  end

  def controller_for_class(controller_class)
    navigation_controller.viewControllers.select { |vc| vc.is_a? controller_class }.first
  end
end
```

Now from anywhere in our code, including in delegate classes that I suggest should be on their own instead of in the view controller, we can call:

```ruby
NavigationManager.shared.navigate_to(SomeController.new)
```

With our setup, we can be sure that `shared` is always going to return the same navigation manager every time.

This works create for classes like `APIManager` or `SessionManager` (which you can see I used above too) as well to create global singletons that make life easier for you and allow you to write the code you want to use.

Using this method is suggested over the conventional Ruby way of using `||=` which is a topic for another time.

## Run It Once Only!

The other case for `Dispatch.once` is if you want to... well... run code only once ever. This might be handy for some methods that you know might be called repeatedly, but there is some of the code that you don't want to be repeated because it would leave you in a weird state.

Lets use a `configure` method on a `UITableViewCell` subclass as an example. This wouldn't be how you would create the subviews, but just for demonstration purposes we'll create them in the configure method, which we don't want to do twice.

```ruby
class MyCell < UITableViewCell
  def configure(data)
    Dispatch.once do
      addSubview(@title = MyLabel.new)
      addSubview(@text = MyText.new)
      addSubview(@image = MyCircleImage.new)
    end

    @title.text = data[:title]
    @text.text = data[:text]
    @image.image = data[:image]
  end
end
```

## I do this a lot

If you watch [MotionInMotion](https://motioninmotion.tv/) then you either may of noticed that I use this quite often, or you may start noticing it more now.

If you've got any comments, feel free to [tweet them to me on Twitter](https://twitter.com/intent/tweet?screen_name=RubyMotionTV&text=Regarding%20%22Grand%20Central%20Dispatch%20Isn't%20Just%20For%20Background%20Queues%22%3A%20).

