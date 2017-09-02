---
layout: post
title: Category Drop-Down in WordPress
author: Daniel
date: 2007-05-24 09:00:15
categories:
- [ Programming, JavaScript ]
- [ Programming, PHP, WordPress ]
tags:
- category
- dropdown
- wordpress
summary: A category dropdown (select list) in WordPress
---

[WordPress][] provides a [template tag][template], [wp_dropdown_categories][cat], that inserts a drop-down list (the HTML &lt;select&gt; element) of categories, where the value of each item is the ID from the database. This works fine if you are not using rewrite rules (AKA "pretty links") - you can construct a URL using the value (?cat=[number]). However, if you use any sort of rewrite rules, this does not work. I recently converted my [personal site][], which uses the Pool theme, to utilize a JavaScript array to assist with displaying category pages.

Here's the code...

_Edit: Code has been moved to [this post][]._

This works for both "pretty" and standard links, as it uses the template tag [get_category_link][link] to specify the link.


[WordPress]: //wordpress.org "WordPress"
[template]:  //codex.wordpress.org/Template_Tags "Template Tags - WordPress Codex"
[cat]:       //codex.wordpress.org/Template_Tags/wp_dropdown_categories "wp_dropdown_categories - WordPress Codex"
[personal]:  //daniel.summershome.org "Daniel J. Summers"
[this post]: /2007/posting-source-code-in-wordpress-take-2.html "Posting Source Code in WordPress, Take 2 &bull; DJS Consulting Tech Blog"
[link]:      //codex.wordpress.org/Function_Reference/get_category_link "get_category_link - WordPress Codex"
