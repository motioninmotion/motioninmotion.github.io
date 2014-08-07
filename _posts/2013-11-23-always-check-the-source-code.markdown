---
title: Always Check The Source Code
layout: post
---

So I did something stupid...

I was doing the prep work for a [MotionInMotion](http://upcoming.motioninmotion.tv) episode on [Frank](https://github.com/moredip/Frank) and [MacBacon](https://github.com/alloy/MacBacon), and while I was setting up the example project, I kept getting this error.

`ACCESSIBILITY DOES NOT APPEAR TO BE ENABLED ON YOUR SIMULATOR. Hit the home button, go to settings, select Accessibility, and turn the inspector on. (RuntimeError)`

Ok, haven't seen that before, I'll do that then.

Run again...

`ACCESSIBILITY DOES NOT APPEAR TO BE ENABLED ON YOUR SIMULATOR. Hit the home button, go to settings, select Accessibility, and turn the inspector on. (RuntimeError)`

WHAT? I just did that...

I got angry at it after spending half an hour Googling, decided I'd try again in a day or two.

*Two Days Later*

`ACCESSIBILITY DOES NOT APPEAR TO BE ENABLED ON YOUR SIMULATOR. Hit the home button, go to settings, select Accessibility, and turn the inspector on. (RuntimeError)`

STOP THAT!!!

Fine I'll post in the [Google Group](https://groups.google.com/forum/#!newtopic/frank-discuss)...

I should probably give some examples of the lines of code the problem is coming from.

Then I started following the code back from where that error is generated... didn't take long.

1. [The line the error is generated on](https://github.com/moredip/Frank/blob/master/gem/lib/frank-cucumber/frank_helper.rb#L417)
2. [The accessibility check](https://github.com/moredip/Frank/blob/master/gem/lib/frank-cucumber/frank_helper.rb#L373)
3. [The server command](https://github.com/moredip/Frank/blob/master/src/AccessibilityCheckCommand.m#L16)

Wait what...

```objc
UIWindow *keyWindow = [[UIApplication sharedApplication] keyWindow];
```

Oh...

I only just generated this project... it has no window yet...

DANG IT!

*Moral of the story*

Always check the source code. You won't need to embarrass yourself in the Google Group.

I've sent in a [pull request](https://github.com/moredip/Frank/pull/257) now.