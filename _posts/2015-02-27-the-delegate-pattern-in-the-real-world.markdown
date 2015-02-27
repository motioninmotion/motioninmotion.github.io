---
title: The Delegate Pattern in the Real World
layout: post
---

When starting to learn how to develop iOS apps (or OS X apps for that matter) an area that trips people up regularly is how Delegates and Data Sources work.

I won't go so far to say this is even near the top of the list for confusing subjects in iOS development, I reserve those spots for technologies like Core Data and Auto Layout. Everyone eventually gets how Delegates and Data Sources work, and they partially understand why they exist. There is a problem with only reaching this level of understanding. It can sometimes be a year or two, maybe longer, before an iOS developer realises how useful the Delegate design pattern used throughout Cocoa Touch is. Creating your delegates for your classes is quite simple and can clean up your interfaces.

## Office Politics

In the real world where everything doesn't eventually turn into ones and zeros but instead is made more from action and planning, we use delegation every day.

Most enterprises and well-established businesses over time have created a kind of process catalogue. This collection of guides and how-tos can appear in the form of an internal wiki, employee training and onboarding, or as is typical for many tasks, the passing down of knowledge between generations of workers.

Business units, whether it is marketing, support, finance, or any other group have some form of hierarchy. Somewhere at the top of that department's employee chart is someone with a title like "Manager of X" or "Department Manager, X". They receive the goals and wants from the higher ups in management and disseminate that information to multiple levels of subordinates.

As the objectives of management get passed through each level, they are filtered repetitively, each time splitting up the objective into something closer to a direct action. This is the world of office politics, something that everyone, even those that have escaped exposure to it, understand quite well. Other than the word "delegate" though, how does this relate to programming for iOS?

## Delegation should be simple

Analogies are always a nice way to learn and understand things, so now that we've learnt all about office politics, how can we create terminology analogous to the terms used in iOS development?

If we think of our application as it is running in the same way we look at a company achieving its goals we can see some similarities.

* Management is the user
* Department managers are the interface or views
* Departments are the targets
* Team managers are the actions
* Workers are the delegates
* Process documentation is a delegate's possible methods

At the top of the food chain is management trying to achieve their goals, just as a user of your application is trying to solve theirs. Users and management are masters of intent and the big picture, focused on utilizing their available resources. For the management, the resources they are in collaboration with are the department managers. In the case of a user, that would be the user interface, our views.

A department manager's job is to interpret and filter out some of the information provided, making it easier to delegate to the department they manage. The interface elements in an iOS application do much the same thing. There is much information available to a view, but they must determine the correct interpretation before passing it down the line. Once the information becomes something more actionable and relevant to a departments' knowledge, a choice can be made about which team (action or method) will handle it.

The task of achieving a goal then becomes the problem of the team manager. They must co-ordinate their team effectively to ensure success. At this point, team managers make a choice. You can either bring in a specialist or firm to accomplish parts of the larger problem being solved or utilize the trained workers that have access to process documentation if it is a relatively common task.

When programming, many developers are used to going with the specialist option. To abstract away complexity to lower levels, we create things like service objects or factories. This is not a bad thing in any way. It gets the job completed, and usually very effectively with the correct choice of specialist or firm. How would a developer accomplish the other option though?

## Internal teams

Bringing work in-house is commonplace once utilising external help stops being cost-effective. The ultimate goal of this process is to make it more affordable to replace resources when they are no longer available. If an external firm decided to cancel a support contract, it could be costly to find a new company. Doing this multiple times is simply unsustainable.

In programming, we have the same concept. To achieve this, we commonly use protocols or interfaces, depending on the language background you have. We define a standard way we expect something that will perform a task to behave. Certain inputs a resource should take, and outputs it will return. We wrap this all up as a list of decisions and actions a resource conforming to a protocol should be able to make.

Some employees have different levels of experience and knowledge, they can also do things in slightly different ways. This is what we expect from classes conforming to a protocol as well. Team managers care only slightly on the method used to achieve a result, and so should our controllers' actions. The idea of different levels of knowledge, junior vs. senior, is analogous to the choice of having optional methods in a protocol.

Delegates should use the instructions they know from training and experience to accomplish a task in the real world, and they should do the same with applications. A team manager should be able to choose a worker they think is appropriate and have them do a job. You should be able to swap them out if need be and trust that the whole company won't fall apart, or even notice.

Using delegates and protocols is an achievable and masterable skill that only takes a small amount of practice. Pushing a little further when creating your next service object to define a protocol for it and decouple it from your view controller is a great first step. You will quickly learn to see when to use, and importantly when not to use, protocols and delegates.
