---
layout: post
title: jQuery in Rails 3
description: Do you want to see jQuery in Rails 3?
---

<img class="right" src="/images/jquery.png" />

Recently, I see more and more people start using Rails with [jQuery][3]. The Rails Core team actually maintains a jQuery [rails.js][2] file. But, Rails 3 doesn't have an option for you to start with jQuery as the default Javascript library.

## Patch

Hence, I decided to patch this feature myself. With my patch, you can start a Rails 3 project with jQuery as the default Javascript library with the `-j` option. And, it accepts both "jquery" or "prototype" as its parameter.

## How

In short, you can start a Rails 3 project with jQuery in this way:

{% highlight bash %}
rails new my_cool_project -j jquery
{% endhighlight %}

And, to skip Javascript at all. You can do:

{% highlight bash %}
rails new my_cool_project --skip-javascript
{% endhighlight %}

which I think is better than the `--skip-prototype` option Rails 3 provides now. Want to see this get into the future Rails 3 release? Do +1 [here][1] at Lighthouse.

Edit: Finally, the commit is [in][4].

[1]: https://rails.lighthouseapp.com/projects/8994/tickets/5613-adding-j-parameter-for-rails-new-command
[2]: http://github.com/rails/jquery-ujs
[3]: http://jquery.com/
[4]: http://github.com/rails/rails/commit/708e09448ba41c45f189c6b1adc989eba8994b20
