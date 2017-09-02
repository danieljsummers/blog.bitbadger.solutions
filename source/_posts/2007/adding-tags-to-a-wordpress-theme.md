---
layout: post
title: Adding Tags to a WordPress Theme
author: Daniel
date: 2007-11-25 22:47:28
categories:
- [ Programming, PHP, WordPress ]
tags:
- tag
- template
- theme
- wordpress
summary: Now that WordPress supports tags, this is how to put them in an existing theme
---

WordPress 2.3 introduced tags, but unless you're using the default theme, your theme (like mine) probably didn't support them.  Nowhere did I find a good example of how to add tags to your theme.  Then, I was playing around with the theme switcher on my personal blog, and discovered that the default theme had tag support.  I looked at it, and it was amazingly simple.

There is a new template tag called ...drumroll...  `the_tags`.  It takes three parameters: how to begin the list, how to separate each tag, and how to end the list.  The tag does not do any output if a post hasn't been tagged, so it can safely sit in your theme until you need it to be active.

Here's how I did it in my personal blog.

{% codeblock lang:php %}
<?php the_tags('<div class="tags">Tags &raquo; ', ' &bull; ', '</div>'); ?>
{% endcodeblock %}

Of course, you could also do it using an unordered list...

{% codeblock lang:php %}
<?php the_tags('Tags<ul class="tags"><li>', '</li><li>', '</li></ul>'); ?>
{% endcodeblock %}

Drop some styling for the "tags" class in your theme's CSS, and you're good to go!

(Well, not quite.  You'll want to make sure to make the same change in your main index template, single post template,
and archive template, so that the tags appear no matter how the user got to the post.)
