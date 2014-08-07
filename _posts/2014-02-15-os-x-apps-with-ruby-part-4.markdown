---
title: "OS X Apps with Ruby: Part 4"
layout: post
---

We finished off [part 3](http://blog.motioninmotion.tv/os-x-apps-with-ruby-part-3) with the app basically completed, but it lacked [polish](https://developer.apple.com/library/mac/referencelibrary/GettingStarted/RoadMapOSX/books/RM_YourFirstApp_Mac/Articles/AddPolish.html#//apple_ref/doc/uid/TP40012262-TP40012101-CH6-SW1), so we’re going to finish things off by formatting the number in the text field so it’s not so long, and finally we’re going fix the layout so it grows and shrinks with the window.

If you haven’t read parts [one](http://blog.motioninmotion.tv/building-os-x-apps-with-rubymotion), [two](http://blog.motioninmotion.tv/os-x-apps-with-ruby-part-2), and [three](http://blog.motioninmotion.tv/os-x-apps-with-ruby-part-3), go back and read them now. You can also find the [source code on GitHub](https://github.com/FluffyJack/TrackMix).

### Announcement

Pre-orders have opened up for the book I’m writing called “RubyMotion for Rails Developers” which will cover iOS and OS X programming the ruby way, and will be a great introduction for both Ruby/Rails developers, and for RubyMotion beginners to mid-level developers. Pre-orders are open under a Pay What You Want pricing model, so go and check it out now at http://book.motioninmotion.tv/ and share it with your friends and followers.

### Add a number formatter

To format the number in the text field automatically, we can give it what is called a number formatter (NSNumberFormatter). There is many attributes we can set to specify how the number formatter works. We want ours to only allow numbers between 0 and 11, and show up to two decimal places. Write the code that sets the formatter (between setting the action and adding the subview).

```ruby
@textField.action = 'takeFloatValueForVolumeFrom:'
@textField.formatter = NSNumberFormatter.new.tap do |f|
  f.minimum = 0
  f.maximum = 11
  f.allowsFloats = true
  f.localizesFormat = true
  f.minimumFractionDigits = 0
  f.maximumFractionDigits = 2
end
@mainWindow.contentView.addSubview(@textField)
```

Now when you run `rake` and slide the slider, you can see that the number in the text field is properly formatted. All that’s left to add polish to now is the layout when the window is resized.

### Auto layout

Apple has given us an extraordinary layout API called [Auto Layout](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html), which lets us do “constraint based layouts”, instead of manually setting the frame, or doing things the old way with springs and struts.

The basic idea is you take two views, and tell auto layout how they relate to each other (matching widths, top position of one view is the bottom of the other view, etc), and then you can add in extra metrics (view one’s left is 20 points from view two’s right).

We can either create constraints manually, or we can use the recommended method of using the [Visual Format Language](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage/VisualFormatLanguage.html), which will then turn the visual format string into all the constraints we want, so we can then add them to the views.

After all the view creation code (so at the bottom of our `buildWindow` method) we add the following to get things started.

```ruby
constraints = []
views = {'text' => @textField, 'slider' => @slider, 'button' => @button, 'superview' => @mainWindow.contentView}
```

This is just some prep work, as we’ll be adding all our constraints into this array to apply them all at once, and we need a hash of our views to give to the visual format strings, which we’ll look at now.

```ruby
constraints += NSLayoutConstraint.constraintsWithVisualFormat('H:|-(>=20)-[text(==100)]-(>=20)-|',
  options: NSLayoutFormatAlignAllCenterY,
  metrics: nil,
  views: views
)
```

To explain what’s going on here, constraints are created using the NSLayoutConstraint class. The `constraintsWithVisualFormat:options:metrics:views:` method takes our layout string, special options for how the constraints work with one another (we’re keeping our constraints in favour of centring things when possible), metrics which we’re not using because we’re just writing them straight into our strings (see the `20` in the string), and the hash of our views, which we use the keys in our layout string.

This visual format string is telling auto layout we want a horizontal layout (`H:`) with at least 20 points of space (`(>=20)`) between the superview (`|`) and the text field which should be exactly 100 points wide (`[text(==100)`). Now we can do almost the same for the slider and mute button, except the slider will be 22 points wide.

```ruby
constraints += NSLayoutConstraint.constraintsWithVisualFormat('H:|-(>=20)-[slider(==22)]-(>=20)-|',
  options: NSLayoutFormatAlignAllCenterY,
  metrics: nil,
  views: views
)
constraints += NSLayoutConstraint.constraintsWithVisualFormat('H:|-(>=20)-[button(==100)]-(>=20)-|',
  options: NSLayoutFormatAlignAllCenterY,
  metrics: nil,
  views: views
)
```

That’s our horizontal layout complete, now we need to specify our vertical layout.

```ruby
constraints += NSLayoutConstraint.constraintsWithVisualFormat('V:|-(==20)-[text(==22)]-(==20)-[slider(>=100)]-(==20)-[button(==22)]-(==20)-|',
  options: NSLayoutFormatAlignAllCenterX,
  metrics: nil,
  views: views
)
```

We start off by telling auto layout this is a vertical layout (`V:`), then the 22 point high text field (`[text(==22)]`) will be exactly 20 points (`(==20)`) from it’s superview (`|`). The slider will be at least 100 points high but is willing to grow bigger than that (`[slider(>=100)]`) is going to have 20 points on the top and bottom. Then finally the mute button is going to be 22 points high (`[button(==22)]`) and be 20 points from the bottom of the superview (`(==20)-|`).

The option is also telling it to try and center everything on the X axis if possible.

Finally lets add our constraints to the contentView of our window.

```ruby
@mainWindow.contentView.addConstraints(constraints)
```

Then run rake and see what happens…

### Problems with auto layout

Alright, so that didn’t work exactly as expected. What happened is auto layout didn’t know how to take the horizontal layouts where there should be “at least 20 points on either” side, and center them like we expected, instead it let the left padding grow, while making sure the right padding was the minimum 20 points. We can add some manual constraints to fix this up though. Try adding these just before the line where we add our constraints to the window’s content view.

```ruby
constraints += [
  NSLayoutConstraint.constraintWithItem(@textField,
    attribute: NSLayoutAttributeCenterX,
    relatedBy: NSLayoutRelationEqual,
    toItem: @mainWindow.contentView,
    attribute: NSLayoutAttributeCenterX,
    multiplier: 1,
    constant: 0
  ),
  NSLayoutConstraint.constraintWithItem(@slider,
    attribute: NSLayoutAttributeCenterX,
    relatedBy: NSLayoutRelationEqual,
    toItem: @mainWindow.contentView,
    attribute: NSLayoutAttributeCenterX,
    multiplier: 1,
    constant: 0
  ),
  NSLayoutConstraint.constraintWithItem(@button,
    attribute: NSLayoutAttributeCenterX,
    relatedBy: NSLayoutRelationEqual,
    toItem: @mainWindow.contentView,
    attribute: NSLayoutAttributeCenterX,
    multiplier: 1,
    constant: 0
  )
]
```

This may look complicated, but all we’re doing is manually creating 3 constraints (the visual format helper is doing this for us with the rest of the code), which say that the text field, slider, and mute button, should have a relation with the window’s content view, where each view’s center x co-ordinate, should be equal to the content view’s center x co-ordinate. This will align all three views horizontally within the window.

Run rake now, and try expanding and shrinking the window and you’ll see that not only does our layout now look great when the window gets bigger, but auto layout has actually worked out that we need to have a minimum size for the window to stop the slider from being less than 100 points, and the padding for the left and right from being smaller than 20 points.

### All done!

That’s our application, and this series done! I hope this was a great introduction for you into learning how to create OS X applications. I’ve had heaps of emails coming in from people saying they’ve absolutely loved this series, so I’m hoping to revisit it and expand on it with more advanced concepts in the future.

For now you can sign up for my screencasts over at [https://motioninmotion.tv/](https://motioninmotion.tv/), where I’ll be covering OS X soon, or you can pre-order my upcoming book [RubyMotion for Rails Developers](http://book.motioninmotion.tv/), which is currently available under a Pay What You Want pricing model.

Please also share these posts around with your friends and followers so they too can begin their journey to learning how to create OS X applications using RubyMotion.