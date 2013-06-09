---
layout: post
title: Powerful and Hidden rake:notes in Rails
categories : [ blog ]
description: Rails like routes.rb for Backbone 
---

There are two really nice `rake` commands that many people do not notice
in Rails. There are `rake notes` and `rake notes:custom`.

When you are writing some code, you might use some notations
in your code to note to yourself that you might want to go back there
to fix certain things, for example, code optimization, refactoring, etc.

You usually use notations like:

{% highlight ruby %}
# REFACTOR: Abstract this out to be a separated class
code_smell

# OPTIMIZE: This algorithm can be better
super_slow_algorithm
{% endhighlight %}

## Usage 

{% highlight bash %}
rake notes         # Enumerate all annotations (use notes:optimize, :fixme, :todo for focus)
rake notes:custom  # Enumerate a custom annotation, specify with ANNOTATION=CUSTOM
{% endhighlight %}

## Output

{% highlight bash %}
app/assets/javascripts/backbone/models/thread.js.coffee:
  * [ 61] [TODO] display something

app/assets/javascripts/backbone/views/muses/add_muse_view.js.coffee:
  * [ 59] [TODO] better way of handling error needed.
  * [ 86] [TODO] need a better way to handle error
{% endhighlight %}

## Bug

But, currently, it only supports certain file types in the latest
version of Rails(3.2.3). Some unsupported files are `scss`, `coffee`,
`js`, and `css`.

My patch to this has been merged to Rails core. So, you have to wait for
the new release to actually see this in effect for those unsupported
file types.

