---
layout: post
title: Incorporating an Akismet Counter Into a WordPress Theme
author: Daniel
date: 2007-08-06 10:14:00
categories:
- [ Programming, PHP, WordPress ]
tags:
- aksimet
- counter
- spam
- theme
- wordpress
summary: Akismet counts the spam it blocks, and that count can be displayed
---

[Akismet][] is, by far, the most popular anti-spam plug-in for WordPress. (It comes bundled with the download, so that gets it market share. But, it's also very, very good.) It comes with a counter that can be put into a WordPress theme. It's attractive, but its light blue color may not integrate well into a given theme.

I went digging around in the source code, and found the line that actually pulls the count from the database. Using this parameter, I was able to integrate a spam count into the sidebar that has a look consistent with the rest of the site.

Here's the code that's in use on the theme on this site.

{% codeblock lang:php %}
<li id="spamstats">
  <h2><?php _e('Akismet-Eaten Spam:'); ?></h2>
  <ul>
    <li><a href="//akismet.com"><?php
			echo(number_format(get_option("akismet_spam_count"))); ?>
			and counting...</a></li>
	</ul>
</li>
{% endcodeblock %}

Of course, line 5 is the important one - that's how to get the number, formatted for whatever locale the server is set up for. (On my [personal blog][djs], the number is up over 1,400!)


[Akismet]: //akismet.com "Akismet"
[djs]:     //daniel.summershome.org "Daniel J. Summers"
