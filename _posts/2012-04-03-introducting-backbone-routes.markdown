---
layout: post
title: Introducing Backbone Routes 
description: Rails like routes.rb for Backbone 
---

I created this [Backbone][1] plugin few months ago for [DailyMuses][2]. At
DailyMuses, we depend heavily on Backbone and concurrent routers call
for a single page in order to have a better way to organize our
codebase.

Since I am coming from the [Rails][3] background, when I first tried to
tackle this problem, I immediately thought of the way how Rails handles
multiple routes with `config/routes.rb`. Hence, I created [Backbone
Routes][4].

With Backbone Routes, you will have a central place to control all
your routing logic for your Backbone app.

## Warnings

This plugin will replace some of the things that Backbone is
doing now.

For example:

{% highlight coffeescript %}
class YourApp.Routers.UsersRouter extends Backbone.Router
  routes:
    "/new": "new"
{% endhighlight %}

`routes` will not longer working once you start using Backbone.Routes
with your Backbone app. So, what Backbone.Routes is useful for?

## Installation

To install this, just load it after Backbone is loaded.

For example in Rails 3.1,

{% highlight javascript %}
//= require jquery
//= require jquery_ujs
//= require underscore
//= require backbone
//= require backbone_routes
{% endhighlight %}

## Features

In fact, if you want to have a central place to control all your routing
logic for Backbone. Backbone.Routes is a good plugin that you should
use.

Example:

{% highlight coffeescript %}
Backbone.Routes.prefix = YourApp.Routers

# Matches from top to bottom.
Backbone.Routes.map
  "/":
    "NavbarRouter": "index"
    "HomeRouter": "index"

  "/signin":
    "NavbarRouter": "index"
    "RegistrationRouter": "signin"

  "/questions":
    "NavbarRouter": "index"
    "QuestionsRouter": "index"
    "SidebarRouter"  : "new_questions"

  "/:nick":
    "NavbarRouter": "index"
    "ProfilesRouter" : "index"
    "SidebarRouter"  : "new_questions"
{% endhighlight %}

So, the first thing you have to set is the `prefix` for Backbone.Routes.
The default is `window`. So, how is it used by Backbone.Routes. If you
take a look at the first route: "/". There are `NavbarRouter` and
`HomeRouter`. So, once you set the `prefix`, inside Backbone.Routes, it
will call:

{% highlight coffeescript %}
Backbone.Routes.prefix = YourApp.Routers

# a safer way than actually calling eval()

# The following code is equivalent to
# new YourApp.Routers.NavbarRouter()
new Backbone.Routes.prefix["NavbarRouter"]() # string to class

# and

# The following code is equivalent to
# new YourApp.Routers.HomeRouter()
new Backbone.Routes.prefix["HomeRouter"]() # string to class
{% endhighlight %}

So, it also means that you don't have to initiate the Routers again
anymore since Backbone.Routes will do the work for you.

And, another cool thing about Backbone.Routes is that it matches routes
from top to bottom. So, for the example above, only `/signin` will get
called. On a traditional Backbone app, you will have to structure your
Backbone app in the way that `/signin` is called before `/:nick` which
is really annoying most of the time.

## Concurrent Routers

So, you might want to ask, what the following code does?

{% highlight coffeescript %}
"/":
  "NavbarRouter": "index"
  "HomeRouter": "index"
{% endhighlight %}

Yes, it is exactly what you think it is. Two routers get called when the
route `/` is hit. Why is this? I do this because I separated the logic
for the navigation bar and the realy homepage. So, my code can be more
modularized in the way that people who work on the navigation bar and
the people who work on the homepage do not have to step on the foot of
each other.

More modularized code also means less commit conflicts and less points
of
failure.

## Caching

Backbone.Routes supports caching too. For example, if you have code
similar as:

{% highlight coffeescript %}
# Matches from top to bottom.
Backbone.Routes.map
  "/":
    "NavbarRouter": "index"
    "HomeRouter": "index"

  "/signin":
    "NavbarRouter": "index"
    "RegistrationRouter": "signin"
{% endhighlight %}

You notice that `NavbarRouter#index` is called at least two times. And,
one
thing is that `NavbarRouter#index` is static. The content of it doesn't
change. So, why should Backbone rerender it everytime?

So, to cache that particular action `NavbarRouter#index`, you can do:

{% highlight coffeescript %}
class NavbarRouter extends Backbone.Router
  cache: ["index"]

  index: ->
    # blah blah blah...
{% endhighlight %}

Notice the `cache` keyword here, it is used to cache the `index` action
in this particular router `NavbarRouter`. Once you use the `cache`
keyword, `NavbarRouter#index` will only be loaded for the first time,
any subsequent call to `NavbarRouter#index` will be cached.

Notice that the caching only work for the subsequent call. If
`NavbarRouter#index` isn't called for the subsequent call. It will be
purged from the cache.

## Help/Issues

The code is extremely simple. Just read the source code if you have any
questions. Or, you can create a GitHub [issue][5], I will look at them as
soon as I have the time.

[1]: http://documentcloud.github.com/backbone/
[2]: http://dailymus.es/
[3]: http://rubyonrails.org/ 
[4]: https://github.com/siong1987/backbone_routes/
[5]: https://github.com/siong1987/backbone_routes/issues/
 
