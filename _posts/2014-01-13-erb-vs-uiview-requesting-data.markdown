---
title: ERB vs UIView - Requesting Data
layout: post
---

## Explaining Cocoa and iOS/OSX Programming Part 2

It’s not just Objective-C as a language that can scare people off, RubyMotion helps solve part of the problem, but developing apps for iOS and OSX can be really confusing coming from a background of programming for the web (e.g. with Ruby on Rails).

This series is going to be dedicated to trying to explain some of the confusing parts, those bits Rails, ASP.NET MVC, Django, etc developers get to and either get scared off by or use completely wrong for a year or two until they get that “Ah-ha!” moment, if they haven’t given up by then.

**Self-promotion**
If you're wanting to learn more about RubyMotion, I run [MotionInMotion](https://motioninmotion.tv/), a weekly released RubyMotion screencast.

## We're not so different UI and ERB

That was a horrible heading... but it's true. Display and accepting data using HTML & ERB or UIViews isn't all that different. Both have the same outcome, display dynamic data to a user, and accept data and actions from a user (though JavaScript makes an appearance for the web guys pretty often too). You're managing a view hierarchy, one way is with DOM nodes, the other is with UIViews and their subviews. And both will (if you're following MVC correctly) be getting data from the controller which it got from some models.

Explaining the hierarchy is kind of simple, though I'll cover it fully in another blog post. You can just think of each view as a DOM node in HTML. One main view (the html or body tag), with subviews defining different parts of the screen (div tags), and other subviews in those or in the root view like labels, text fields, buttons (paragraphs, headings, inputs, anchors, buttons). Each view (node) then knows what subviews (child nodes) it needs to display. FYI, event bubbling works extremely similarly to JavaScript's event bubbling too.

Displaying and accepting data, same in both worlds, you have views (elements) designed specifically for accepting and displaying certain kinds of data. Then if you want to display a conceptual unit of data like a bank transaction, you can group a bunch of these standard views together, laying them out and styling them to suit the visual hierarchy needs.

Where things get a little different though, is controlling where the data comes from.

## How we do it in ERB

Let's take a simple example of a list of todos in a Rails app.

**todo_controller.rb**

```ruby
def index
  @todos = Todo.all
end
```

**todos/index.html.erb**

```erb
<div id="todo-list">
<% @todos.each do |todo| %>
  <div class="todo-item">
    <p class="todo-item-title"><%= todo.title %></p>
    <p class="todo-item-metadata"><%= todo.due_date %>, <%= todo.priority %> Priority</p>
  </div>
<% end %>
</div>
```

Boilerplate stuff, but there is something wrong with this code from the perspective of Apple's MVC pattern... the view is directly communicating with the model.

This isn't a bad thing on the web! Not at all, in fact it's almost 100% necessary, but the way things are done on iOS and OSX is designed specifically to make sure view objects are not communicating with model objects, it's all done through the controller.

## Well how do you display stuff then???

The basic idea is instead of setting an instance variable and the view just grabbing what it needs (though I'm guilty of doing the equivalent in an app or two for iOS), data gets to the view in one of two ways.

The first way is the view will ask the controller questions like "how many todos are there?" so it can work out the height of the scrollable area in a list (called a UITableView), or "what should I display in row 5 of the table?", then when the controller gets these questions, it talks to the models and then lets the view know "there are 42 todos" or "here is the cell for row 5 all filled up with the data for that row". This is called a data source, and usually the controller will set itself to be the data source for a view it controls.

A lot of the time this provides both the benefits of separation of concerns, and especially when it comes to displaying collections of data, it helps with limiting memory usage. I've got two episodes dedicated to delegates and data sources if you want to look into them further. [Episode 2](https://motioninmotion.tv/screencasts/2) and [Episode 3](https://motioninmotion.tv/screencasts/3).

The second way data gets displayed, is simply the controller will set an attribute on the view. For example, when you're looking at a single todo, all the todos details are up on the screen. When the controller creates the todo view with all it's subviews, you can do something like `self.todoView.titleLabel.text = self.todo.title`.

So the views provide methods to set data, and the controller uses those methods to give it to them. This way while the view cares about rendering text it's given, the controller worries about getting the text to be rendered to the view.

Let's take a look at some code for these two scenarios. First up, we'll take a look at a table view displaying the list of todos.

```ruby
class TodosController < UITableViewConroller # subclass of UIViewController that handles lists of data
  
  def numberOfRowsInSection(section) # lists can be grouped into sections, we only have one though
    Todo.count
  end

  def tableView(tableView, cellForRowAtIndexPath:indexPath) # an index path is a combination of the section and the row in the section
    todo = Todo.find(indexPath.row)

    # I won't show the code because it will be pointless without knowing what it does but basically it goes like this
    # Create the cell view
    # Set the title and maybe the subtitle on it using the todo's data
    # Return the cell, which will let the table view display it
  end
end
```

Now that's a bunch of code, and if you don't know anything about table views or much iOS programming, you will probably just get the basic idea from the comments, but you can see how the table view is asking the controller questions by using the methods it defines. There is a bunch of other questions it can ask too, check out the [UITableViewDataSource Protocol](https://developer.apple.com/library/ios/documentation/uikit/reference/UITableViewDataSource_Protocol/Reference/Reference.html).

It might look complicated to some, but this is the iOS boilerplate code for displaying a list, and it is INCREDIBLY memory efficient, and when you're trying to debug something you know exactly where to look.

The next example will be for the screen that shows a single todo.

```ruby
class TodoDetailController < UIViewController # basic controller with a DIY view
  attr_accessor :todo, :todoView

  def initWithTodo(todo)
    super
    self.todo = todo
    self
  end

  def viewDidLoad
    
    self.todoView = TodoView.new
    self.todoView.titleLabel.text = self.todo.title
    self.todoView.dueDateLabel.text = self.todo.dueDate
    self.todoView.priorityLabel.text = "#{self.todo.priority} Priority"
    self.todoView.descriptionTextArea.text = self.todo.description
    self.view.addSubview(self.todoView)

  end
    
end
```

Now this may look bulky, but because a controller is (99% of the time) just dealing with the one screen, the controller is focused just on displaying this particular todo view anyway. You can see that there is two different controller classes for what would usually in Rails be just a different action in the one controller, this is because it's a "View Controller" not just a "Controller", it's important to make the distinction.

The way we might use this controller, is when a row in the `TodosController` is tapped, that controller will init a new `TodoDetailController` by calling `TodoDetailController.alloc.initWithTodo(self.todo)`. Not entirely important to be able to understand the code fully, just the idea of what it does in essence.

Hopefully now you get the idea conceptually, if not practically, of the differences between rendering data, and rendering a configured view that can ask for data as its needed.

## Still didn't make sense?

I’m really determined to find a way to explain this stuff to web framework users. If you’re still confused, please email me (info@fluffyjack.com) with what you’re confused about so I can try and find a better explanation so I can then help the masses.