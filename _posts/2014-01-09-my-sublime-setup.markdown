---
title: My Sublime Setup
layout: post
---

I got a [tweet from René](https://twitter.com/shirkrin/status/420950423420563457) asking about my Sublime theme and setup, so here is a short post basically just sharing my config files.

Keep in mind, Sublime isn't my main editor, just the one I use for the [screencasts](https://motioninmotion.tv/). I'm actually a vim user.

## User Settings File

So I'm using the Twilight colour scheme, and the [Soda](http://buymeasoda.github.io/soda-theme/) theme to make the rest of Sublime match, which is pretty cool. I really like Soda.

I've also setup simple fullscreen because that's how I like my iTerm and Sublime. I also turned off a bunch of annoying stuff like create window at startup, and drag text.

```json
{
	"auto_complete_commit_on_tab": true,
	"bold_folder_labels": true,
	"color_scheme": "Packages/Color Scheme - Default/Twilight.tmTheme",
	"create_window_at_startup": false,
	"drag_text": false,
	"font_size": 13,
	"highlight_line": true,
	"highlight_modified_tabs": true,
	"ignored_packages":
	[
		"Vintage",
		"RubyMotionBuilder"
	],
	"remember_open_files": false,
	"shift_tab_unindent": true,
	"tab_size": 2,
	"theme": "Soda Dark.sublime-theme",
	"translate_tabs_to_spaces": true,
	"use_simple_full_screen": true,
	"word_wrap": true,
	"wrap_width": 120
}
```

## My package control settings (so... my packages)

```json
{
	"installed_packages":
	[
		"Emmet",
		"RubyMotionBuilder",
		"SnippetMaker",
		"Theme - Soda"
	]
}
```

## The single key binding so I can do cmd+enter for full screen

```json
[
  { "keys": ["super+enter"], "command": "toggle_full_screen" }
]
```