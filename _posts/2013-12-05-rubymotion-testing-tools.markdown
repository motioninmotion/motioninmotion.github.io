---
title: RubyMotion Testing Tools
layout: post
---

I’m working on some episodes about testing for the launch of [MotionInMotion](http://upcoming.motioninmotion.tv/) and so I decided, I’ll write a quick post about the testing tools out there at the moment.

**UPDATE**: Here is the episode on [BDD with Frank and MacBacon](https://motioninmotion.tv/screencasts/4)

### MacBacon

Most of you should be familiar with [MacBacon](https://github.com/alloy/MacBacon), it’s the testing tool that comes shipped with RubyMotion. It’s a “small RSpec clone” but it’s really “a RubyMotion port of the small RSpec clone called bacon”.

Being an RSpec fan, this is of course my go to testing tool, and all I’ll use on small apps.

**Some links for you:**

* [RubyMotion Guide](http://www.rubymotion.com/developer-center/articles/testing/)


* [Screencast from Motioncasts.tv](http://motioncasts.tv/10-tdd-with-rubymotion/)


* [GitHub Repo](https://github.com/alloy/MacBacon)


**NOTE**: I’ll be covering this in one of the launch episodes.

### Kosher Bacon

There is a less well known kosher alternative to MacBacon called [Kosher Bacon](https://github.com/kastiglione/kosher_bacon), which is made for all you MiniTest::Unit/Test::Unit fans out there.

It’s not a complete port of MiniTest::Unit or Test::Unit, it’s simply putting a layer over MacBacon so you can write your tests the way you like, so (as far as I can tell) you have access to everything MacBacon provides.

## Cucumber Fans

When it comes to doing cucumber style acceptance testing with RubyMotion, you’ve got two great tools. [Frank](http://www.testingwithfrank.com/) and [Calabash](http://calaba.sh/).

### Frank

I personally have a preference for Frank, mainly because it can be used to test both iOS and OS X apps.

It’s maintained by the awesome [Pete Hodgson](https://twitter.com/ph1) and has it’s own great community behind it. Really easy to setup, especially using the [motion-frank](https://github.com/cyrusinnovation/motion-frank) gem, takes less than a few minutes. Just don’t make [the same mistake](http://blog.motioninmotion.tv/always-check-the-source-code) I did one day (well several days) without thinking.

**Some links for you:**

* [GitHub Repo](https://github.com/moredip/Frank)


* [Google Group](https://groups.google.com/forum/#!forum/frank-discuss)


* [Free Screencasts](http://www.testingwithfrank.com/screencasts.html)


* [And some presentations](http://www.testingwithfrank.com/presentations.html)


* [motion-frank](https://github.com/cyrusinnovation/motion-frank)


* [A (kinda old) setup article](http://calebcohoon.blogspot.com.au/2012/06/test-rubymotion-apps-using-cucumber.html)


**NOTE**: I’ll be covering this in one of the launch episodes.

### Calabash

Though I prefer Frank, and though Calabash is iOS only (well Android too), it does have a benefit. [The Xamarin Test Cloud](http://xamarin.com/test-cloud), which is great if you want to get a CI setup going for multiple iOS devices.

Calabash can’t do OS X apps, and doesn’t have [Symbiote](http://blog.thepete.net/blog/2011/05/01/inspect-state-of-our-running-ios-apps/), but it really does cover all your iOS acceptance testing, which for a lot of you I know that’s all you’re interested in.

**Some links for you:**

* [GitHub Repo](https://github.com/calabash/calabash-ios)


* [Google Group](https://groups.google.com/forum/#!forum/calabash-ios)


* [motion-calabash](https://github.com/calabash/motion-calabash)


* [Xamarin Test Cloud](http://xamarin.com/test-cloud)


## Tools to help our testing

It wouldn’t be part of the ruby community if we didn’t have a bunch of extra tools to help us would it.

### Stubbing and Mocking

There is several tools for your mocks and stubs, [motion-stump](https://github.com/siuying/motion-stump), [facon](https://github.com/chuyeow/facon), and for stubbing out your web requests [webstub](https://github.com/mattgreen/webstub).

### Other stuff

For creating fixtures for files and stuff (like a cache file, or database) you’ve got [motion-fixtures](https://github.com/farcaller/motion-fixtures).

If you’re wanting to pretty up the output in the terminal there is [awesome_print_motion](https://github.com/michaeldv/awesome_print_motion) and [motion-colorize](https://github.com/clayallsopp/motion-colorize).

And finally, if you’re a guard fan: [guard-motion](https://github.com/mordaroso/guard-motion).

I’ll probably keep updating this over time, but when you’re looking for tools, always checkout the [RubyMotion Wrappers website](http://rubymotion-wrappers.com/) and [search for “rubymotion” on GitHub](https://github.com/search?q=rubymotion).