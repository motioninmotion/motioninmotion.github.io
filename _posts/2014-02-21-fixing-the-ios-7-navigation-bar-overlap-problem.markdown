---
title: Fixing the iOS 7 Navigation Bar Overlap Problem
layout: post
---

There has been a problem that has been plaguing iOS developers since the pretty transparent navigation bars came out in iOS 7, something that when I ask people why it happens, they have absolutely no idea.

There is a simple fix that everyone has been using, but it's a hack and many developers have just been using it without understanding the problem.

```ruby
self.edgesForExtendedLayout = UIRectEdgeNone
```

Put that in your controller and suddenly it just works... kinda. Now the nav bar has no point to being transparent, which defeats the purpose. The views that should be behind it, our root view, and it's background colour, aren't showing through anymore.

Honestly, you might not care, this might be enough for you, but it's important you still understand the problem, so read on. If you've done web development, this is sorta like `overflow: hidden` in CSS. Does the job, but it's still kind of a "hack".

So the problem is that when you're view's frame is set, unlike in iOS 6, the frame still encompasses the area underneath the navigation bar.

What you might not have realised, is if a `UIScrollView`, or a subclass like `UITableView`, is the root view for your controller, it will have it's `contentInset` property set correctly, allowing for the frame to be underneath the navigation bar, but the actual origin it starts drawing from is from the bottom of the navigation bar instead.

This means we can very simply fix the problem of the navigation bar overlapping by making our root view a `UIScrollView` (or subclass of) instead, while still letting our background colour shine through.

You can make your root view a `UIScrollView` by defining the `loadView` method, where your root view, and it's subviews, should be being created if you're creating your views programmatically.

```ruby
class CustomViewController < UIViewController
  def init
    super
    self.title = "Custom VC"
    self
  end

  def loadView
    # DO NOT CALL SUPER

    self.view = UIScrollView.new
    self.view.backgroundColor = UIColor.redColor
    
    self.view.addSubview(
      UIView.new.tap do |v|
        v.backgroundColor = UIColor.blueColor
        v.frame = [[0, 0], [320, 100]]
      end
    )
  end
end
```

If you try this out, you'll get this.

![iOS Simulator Screen shot 21 Feb 2014 11,36,19 pm](/assets/3q3y1841073v3D0c302G3a3z32151o0j.png)

Notice the red shining through the navigation bar? Notice that we set that blue view's origin to be the top left corner? Everything is sitting fine and working how Apple expected us to use the transparent navigation bars.

This has the added bonus of giving us an easier way to expand our layout beyond the screen too now that we're using a scroll view, but you might not need this anyway, instead you can just use it as a replacement for `UIView` that can have it's `contentInset` set.

I haven't looked into the memory differences for this, but my expectation is it would be extremely minimal.

If you would like to learn more about RubyMotion, check out my screencasts at https://motioninmotion.tv/ or my upcoming book [RubyMotion for Rails Developers](http://book.motioninmotion.tv/), which is available for pre-order now, under a Pay What You Want pricing model, with part 1 almost finished and due to be released soon.