---
title: The Secret Life Of App.alert()
layout: post
---

I was a bit stuck this week on what to write about, then I got this from [@markrickert](https://twitter.com/markrickert)

> @FluffyJack I'm always digging into source for App.alert().
> It's so powerful, but there's barely any documentation for it on bubblewrap.io

When I actually dived into the source code of [bubblewrap's UIAlertView](https://github.com/rubymotion/BubbleWrap/blob/master/motion/ui/ui_alert_view.rb), I was actually surprised by how much more was in there than I knew about.

Also this got me thinking, if you're struggling with something or have something you'd like me to write about, just [tweet me](https://twitter.com/FluffyJack).

![iOS Simulator Screen shot 10 Dec 2013 8,56,02 pm](/assets/1D1L0h3q0N3j2L061s1O0v3u0Y3y0C3b.png)

This is the login and password alert ([UIAlertViewStyleLoginAndPasswordInput](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertView_Class/UIAlertView/UIAlertView.html#//apple_ref/doc/c_ref/UIAlertViewStyle)) which is a great alternative to creating a whole UI yourself for your users to login to your app.

My favourite thing about it, how little code it takes.

```ruby
alert = BW::UIAlertView.login_and_password_input(title: 'Login')
alert.show
```

Done. That's displaying it sorted. Beautiful! Ok, that's really not that helpful. We can't do much with that. Lets try again.

```ruby
alert = BW::UIAlertView.login_and_password_input(title: 'Login') do |a|
  APIExample.authorize(a.login_text_field.text, a.password_text_field.text) unless a.clicked_button.cancel?
end
alert.show
```

Now that's the kind of Rubyness I like to see.

You can do several kinds of 'alerts'. There is the plain one you're used to with a title, message, and some buttons. Then you can also use one of 3 different kinds of input ones like the login one I showed you. A normal text input, 'secure' text input (password looking field), and the login and password alert. This gives you a really great range of input and feedback options without the need of creating a UI for certain small tasks. I also must say, they input related ones look A LOT better in iOS 7 than iOS 6!

If you're looking to just quickly put something on the screen though...

```ruby
App.alert('Oops!', message: 'You forgot an email')
```

Nice and simple. Any options you can pass to `BW::UIAlertView` can be passed to `App.alert`, like buttons. You can also use a block to handle it being closed too.

Alrighty, I reckon you're probably getting the basic gist of what you can do with `App.alert` and `BW::UIAlertView` now, so here is some 'docs' I've written up for it. Enjoy!

# BW::UIAlertView

First step as with any part of [bubblewrap](http://bubblewrap.io/) is of course adding the gem.

```ruby
gem 'bubble-wrap'
```

Or if you're not using Bundler, just run `gem install bubble-wrap` then in your Rakefile add one of these.

```ruby
# All of bubblewrap
require 'bubble-wrap'

# Or just the core and UI stuff
require 'bubble-wrap/core'
require 'bubble-wrap/ui'
```

Now you're ready to go with bubblewrap.

## Stock standard BW::UIAlertView

Though BW::UIAlertView has a lot of methods for creating different kinds of alerts, all of them are just creating a new BW::UIAlertView, which you can do with:

```ruby
BW::UIAlertView.new.show
```

That's going to do basically nothing until you give it some details of what you want to show in it and what buttons there will be, so we'll take a look at some of the options.

### options[:title]

![iOS Simulator Screen shot 11 Dec 2013 1,01,47 pm](/assets/0E2l3L3I0t3t18323K2d42330M2k4727.png)

By passing this along to `BW::UIAlertView.new` you can set the larger bolder text you'll sometimes see in alerts.

```ruby
BW::UIAlertView.new({
  title: 'My Title'
}).show
```

You might want to show a bit more information though, which leads us to...

### options[:message]

![iOS Simulator Screen shot 11 Dec 2013 1,03,02 pm](/assets/2B08063l230G13000e2C231u2u302M3U.png)

This is the option for the smaller longer text you see in other alerts, and is where you can give the user a bit more detail about what you're alerting them of.

```ruby
BW::UIAlertView.new({
  title: 'My Title',
  message: 'Welcome to my awesome little alert view! Have an nice day!'
}).show
```

But you need to give your user a way to close the alert.

### options[:buttons]

![iOS Simulator Screen shot 11 Dec 2013 1,07,00 pm](/assets/1N1n3c1O2S1m0X283140301Q2E1F340c.png)

You can provide all the buttons you'd like to show the user, though I don't recommend more than a cancel/ok button, and an action button, otherwise just the cancel/ok button.

```ruby
BW::UIAlertView.new({
  title: 'My Title',
  message: 'Welcome to my awesome little alert view! Have an nice day!',
  buttons: ['OK', 'Learn More']
}).show
```

We'll look at one more option before we start actually doing something with our alerts.

### options[:cancel_button_index]

This option lets you set which is the 'cancel' button out of your buttons. It's usually the button that just closes the alert and does nothing as a reaction like "OK", "Cancel", or "No". It's almost always 0 unless you want to confuse your users with putting the non-destructive button in a place they're not used to (hint: don't do that). Also if you only have the one button, no need to worry about this.

```ruby
BW::UIAlertView.new({
  title: 'My Title',
  message: 'Welcome to my awesome little alert view! Have an nice day!',
  buttons: ['OK', 'Learn More'],
  cancel_button_index: 0
}).show
```

Now we're ready to actually start handling the interaction!

## BW::UIAlertView callbacks and it's block

BW::UIAlertView lets you give it a block, this will just assign that block to the `on_click` callback.

```ruby
BW::UIAlertView.new({
  title: 'My Title',
  message: 'Welcome to my awesome little alert view! Have an nice day!',
  buttons: ['OK', 'Learn More'],
  cancel_button_index: 0
}) do |alert|
  if alert.clicked_button.cancel?
    puts 'Canceled'
  else
    puts 'They want more!'
  end
end.show
```

Or the alternative to this is to pass the callback in as one of the options.

```ruby
callback = lambda do |alert|
  if alert.clicked_button.cancel?
    puts 'Canceled'
  else
    puts 'They want more!'
  end
end

BW::UIAlertView.new({
  title: 'My Title',
  message: 'Welcome to my awesome little alert view! Have an nice day!',
  buttons: ['OK', 'Learn More'],
  cancel_button_index: 0,
  on_click: callback
}).show
```

Just a matter of preference really.

There is also several other callback options, which are just abstractions for the [UIAlertViewDelegate](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertViewDelegate_Protocol/UIAlertViewDelegate/UIAlertViewDelegate.html).

* [:will_present](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertViewDelegate_Protocol/UIAlertViewDelegate/UIAlertViewDelegate.html#//apple_ref/occ/intfm/UIAlertViewDelegate/willPresentAlertView:)
* [:did_present](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertViewDelegate_Protocol/UIAlertViewDelegate/UIAlertViewDelegate.html#//apple_ref/occ/intfm/UIAlertViewDelegate/didPresentAlertView:)
* [:on_system_cancel](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertViewDelegate_Protocol/UIAlertViewDelegate/UIAlertViewDelegate.html#//apple_ref/occ/intfm/UIAlertViewDelegate/alertViewCancel:)
* [:enable_first_other_button?](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertViewDelegate_Protocol/UIAlertViewDelegate/UIAlertViewDelegate.html#//apple_ref/occ/intfm/UIAlertViewDelegate/alertViewShouldEnableFirstOtherButton:)
* [:on_click](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertViewDelegate_Protocol/UIAlertViewDelegate/UIAlertViewDelegate.html#//apple_ref/occ/intfm/UIAlertViewDelegate/alertView:clickedButtonAtIndex:)
* [:will_dismiss](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertViewDelegate_Protocol/UIAlertViewDelegate/UIAlertViewDelegate.html#//apple_ref/occ/intfm/UIAlertViewDelegate/alertView:willDismissWithButtonIndex:)
* [:did_dismiss](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertViewDelegate_Protocol/UIAlertViewDelegate/UIAlertViewDelegate.html#//apple_ref/occ/intfm/UIAlertViewDelegate/alertView:didDismissWithButtonIndex:)

Which are all fairly self explanatory, but I've linked each to the respective UIAlertViewDelegate documentation.

## UIAlertView Styles

There are 4 options for which style of UIAlertView you would like to use. The first is the default, and is rightly called `:default`, but you also have access to `:plain_text_input`, `:secure_text_input`, and `:login_and_password_input`.

You can set which style you would like using the `:style` option when creating your `BW::UIAlertView`.

```ruby
BW::UIAlertView.new({
  title: 'Your Name',
  message: 'Please enter your Full Name',
  buttons: ['No', 'Save'],
  cancel_button_index: 0,
  style: :plain_text_input
}).show
```

`:plain_text_input` will give you a single text field, `:secure_text_input` provides you with a password style input field, and `:login_and_password_input` gives one of each.

You can also get and set the style using the `style` attribute, just do it before you `show` it.

```ruby
alert = BW::UIAlertView.new
alert.style = :secure_text_input
puts alert.style
alert.show
```

### Accessing the input

These 3 other styles aren't very handy unless you can actually get to what the user entered, so bubblewrap gives you 4 method to access that information.

```ruby
# For the :plain_text_input
alert.plain_text_field.text

# For the :secure_text_input
alert.secure_text_field.text

# For the :login_and_password_input
alert.login_text_field.text
alert.password_text_field.text
```

Use these in your callbacks to get access to what the user entered. This is also possible with the `cancel_button_index`.

## Sensible defaults

That's everything that BW::UIAlertView can do, but it goes a bit further in helping you do some of these things a bit easier by providing you with a 4 creation methods that give you some sensible defaults for the 4 kinds of alerts.

```ruby
# The default - default style and an 'OK' button
BW::UIAlertView.default(options, &block)

# Plain text input - plain text input style, 'Cancel' and 'OK' buttons, and cancel index of 0
BW::UIAlertView.plain_text_input(options, &block)

# Secure text input - secure text input style, 'Cancel' and 'OK' buttons, and cancel index of 0
BW::UIAlertView.secure_text_input(options, &block)

# Login and password input - login and password input style, 'Cancel' and 'Log in' buttons, and cancel index of 0
BW::UIAlertView.plain_text_input(options, &block)
```

Very sensible defaults indeed. You can pass all of the same options that you can to `BW::UIAlertView.new`.

## App.alert()

Bubblewrap also gives you an even more convenient option if all you want to do is quickly display an alert. `App.alert()` will create and show a `BW::UIAlertView.default`, with one "OK" button, and a cancel index of 0.

There is several ways to use it.

```ruby
# Super basic
App.alert('My Title')

# With some options
App.alert('My Title', message: 'My Message')

# With different cancel text
App.alert('My Title', 'Cancel')

# Cancel text and options
App.alert('My Title', 'Cancel', message: 'My Message')

# And if you want to change attributes between creating it and showing it, pass a block
App.alert('Login', buttons: ['Cancel', 'Login']) do |alert|
  alert.style = :login_and_password_input
end
```

Keep in mind that the block works differently with `App.alert()` than `BW::UIAlertView.new`. The block for `App.alert()` lets you customise the alert before `show` is called, instead of being a shortcut for an `:on_click` callback. So in this case, if you want a callback, you'll need to use the syntax I showed you earlier.

## That's all!

That's it for `BW::UIAlertView` and `App.alert()`.

Keep in mind this `BW::UIAlertView` is basically just providing a more convenient syntax for working with alerts, all this is possible with the standard `UIAlertView` with a bit more code (not too much more). So if you don't want to use bubblewrap, you can still enjoy all of this, you just need to read up on [UIAlertView](https://developer.apple.com/library/ios/documentation/uikit/reference/UIAlertView_Class/UIAlertView/UIAlertView.html).