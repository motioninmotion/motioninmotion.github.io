---
title: The great RubyMotion debate - snake_case vs cameCase
layout: post
---

Earlier today [I posted a link to an article on Twitter](https://twitter.com/RubyMotionTV/status/439209622218366977) from [The Frontier Group](https://twitter.com/frontiergroup), which was talking about their experiences with RubyMotion, great article, but when I tweeted the link, it sparked some discussions between me and other people, and other groups of people which those discussions then got brought up with me.

These discussions were of course on the somewhat controversial topic of snake case vs camel case, which the RubyMotion community seems to have mainly adopted using snake case (as you can easily see in our gems), I'd say mainly due to the large amount of developers in the community coming from Ruby/Rails backgrounds.

My belief though is that this is a surface symptom of a larger problem, but before we look at that, let's take a look at both sides of the debate.

## The Great Debate

RubyMotion being the Ruby toolchain it is, using camel case for methods and variables can look pretty "unnatural", as Ruby uses snake case for everything. This has lead to gems like ProMotion and RMQ replacing some of the Cocoa methods like `viewDidLoad` with more Rubyesque alternatives like `on_load`, and lead to developers using snake case in their own code, unless of course they're using Cocoa methods or defining them for say a delegate.

The other side of the argument is that, though we're using Ruby, we're now in "Cocoa land", where things are camel case, and arguably, unless the majority of your work with Cocoa classes is through gems, a very sizeable proportion of the code you write will in fact be using camel case because they're the classes we're working with most of the time. Frontier Group's article expresses their opinion that documentation look up and misuse/misunderstanding of methods like `on_load` using `viewDidLoad` can lead to issues, mainly for beginners.

Here is an excerpt from the [article](http://blog.thefrontiergroup.com.au/2014/02/reflecting-on-rubymotion-experiences-p1/), **these aren't my opinions, I'll get to mine later**.

> I have a bunch of problems with this practice. Firstly: Cocoa Touch already provides a `viewDidLoad` method, complete with comprehensive documentation, and tonnes of examples available online. Good luck to a newbie developer who isn’t aware that `on_load` and `viewDidLoad` are equivalent, trying to find out how to use the method effectively.
>
> Secondly: What does having snake_case get us here? It feels a lot like a bunch of Rails programmers refusing to let go. Ruby on its own, and in the context of Rails, uses snake_case as a standard. A standard, not a requirement. However: we aren’t working in straight Ruby, nor are we working in Rails. We’re using RubyMotion, a Ruby toolchain that communicates directly with Cocoa Touch. Cocoa Touch, of course, being a massive framework written in Obj-C that uses camelCase as a standard.
>
> If you move to another country, you learn their language. I think it’s the same idea here. We are trying to work in the Cocoa Touch land, so we should use camelCase. Where snake_case has the only benefit of looking familiar to Rubyists and Rails devs, it has the drawbacks of forcing you to either:
>
> Write an interface to all of Cocoa Touch that changes every single method from camelCase to having a snake_case equivalent, or
> Mix and match your snake_case methods with Cocoa Touch’s camelCase method.
>
> Both of these options are bad.

## The REAL Problem

My opinion in this whole matter is that this is a need for several things, consistency, education, and clarity. The main part being education.

I don't think **YOU** should write abstractions around methods like `viewDidLoad` just so you can use `on_load` instead, but a framework doing so makes sense, that's the point of a framework, make things work out of the box in easier ways. I am a big fan of frameworks that stay out of my way by just taking care of all that boilerplate stuff, so I can focus on my business logic and interactions.

What any frameworks need to do though, is educate their users on how they're doing things like this, because it's important. This is a quote from a conversation I was having with [Todd Werth](https://twitter.com/twerth) when I got the idea to write this article.

> The snake case vs camel case thing is an odd topic. On one hand it helps discern "ruby" code from the Cocoa code by using snake case, which has a great effect for consistency, but on the other hand that doc look up thing is fairly important for people using IDEs or that have Dash integrated into their editor, something I hadn't thought of too much for beginners. In reality it comes down to education and clarity. I do prefer to have the snake case stuff in RMQ and ProMotion because they're Ruby frameworks, but I'm always going to be an advocate of the idea behind "learn JavaScript not jquery, and use jquery as a tool to learn more and better your code".

The important take away is that last bit: **"learn JavaScript not jquery, and use jquery as a tool to learn more and better your code"**.

This applies to any kind of framework or toolset, be it jQuery, Rails, ProMotion, Wordpress, etc. It's important that you understand at least the foundations of what you're working with, in this case it's Ruby and some of the core iOS and OS X frameworks.

Education is the problem, and this debate is just a symptom with arguments popping up because we might be not educating the new comers enough. Snake case vs camel case is a matter of personal choice or an organisation's/team's choice, which as developers, being the opinionated breed we are, there is going to be debates about this kind of thing. When it comes to thinking of arguments in debates like these though, make sure to think if your argument is linked to education, because that might be an opportunity for you to make things better.

I'm certainly not saying that when Jordan wrote this he was wrong, quite the opposite. He is absolutely, undoubtably right, it does make learning the foundations harder in some cases for beginners. As their team members though, or members of the community, or the creators of gems, we can be pointing them in the right direction when these situations occur.

## Consistency and Clarity

These are points that I can really get behind when it comes to the choice of casing, but it's different in the RubyMotion world.

If you choose to shift paradigms completely to the Apple way, then you're making your code look consistent with Apple's, which is fantastic until... you use a RubyMotion gem. Are you really going to lock yourself off from the majority of the RubyMotion gems?

The other option, is heavily employ snake case, you think your code is going to be the normal pretty snake case Ruby, and the frameworks are helping you with your cause until... time to use Apple's frameworks. Are you really going to write abstractions and use tons of gems just so you can keep your snake casing?

My solution, take a step back, and realise, it's not going to kill you to mix the two, in fact I've started using the mixture to my advantage.

Code abstraction isn't about the casing you use in your code, it's about making your code more expressive, and in a lot of cases, hiding away large boilerplate implementation details so you can focus on behaviour.

I've created consistency in my use of snake case and camel case for certain types of classes, and as always with Ruby, I make my code clear by focusing on behaviour and writing tests first, splitting up large methods as need be.

Consistency doesn't have to be so broadly applied to our code, and clarity is found through being expressive with your code, not which casing you use.

## Summary

My personal choice is:

- I'll use snake case in **my** code
- When a RubyMotion framework uses snake case, it's a nice bonus, but not a deciding factor
- I'm fully aware my RubyMotion code is going to have a mix of snake case and camel case, I'm ok with that
- Both the Ruby way and the Apple way have merit, I'll decide which way is best for me when faced with a problem
- If I'm doing something very Cocoa-y, I'll probably use camel case, quite often in my delegates

