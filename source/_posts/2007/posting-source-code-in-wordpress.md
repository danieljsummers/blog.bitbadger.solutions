---
layout: post
title: Posting Source Code in WordPress
author: Daniel
date: 2007-05-30 14:45:10
categories:
- [ Programming, PHP, WordPress ]
tags:
- bennet mcelwee
- plug-in
- source
- wordpress
summary: A plug-in for WordPress makes posting source code samples easy
---

Traditionally, posting source code in a WordPress blog, especially if it were HTML or PHP, was problematic. There are a few reasons for this...

* WordPress is very good about ensuring that it outputs valid XHTML, so it strips mis-matched and invalid tags. This is usually desirable, but it wreaks havoc with HTML and XML code posts.
* Since WordPress is coded in PHP, blocks of PHP will attempt to execute.
* White space is collapsed, which can kill any readability that the user has set out; the "visual editor" ([TinyMCE][]) does its best to create efficient HTML. This can actually break spacing-oriented languages such as Python.

So how do you do it? It's actually pretty easy, using the [Code Markup][] WordPress plugin. You can download the plugin from that link, and upload it to <tt>./wp-content/plugins</tt>. The instructions on the website are critical - the "visual" editor will not allow the code to come through unscathed. However, the visual editor only corrupts the code if you actually save it; what I have done is write the post using the visual editor, then disable it and put the code in. It's the best of both worlds! (Make sure that, once you have the code in, you don't edit the post using the visual editor - unless you have a good backup...)

<del>The post below this one (about category lists in WordPress) was done using this plugin.</del> _(This isn't running in WordPress any more.)_ In addition, you can use the <tt>&lt;span&gt;</tt> tag to do the color-coding. I created a few CSS classes ("key" for keywords, "func" for functions, "attr" for attributes, "embed" for embedded language), and used them to accomplish the color coding. It seems that it would be pretty easy to write another plugin that used a list of keywords to do this syntax highlighting; maybe that's a challenge for another day.

A big "atta boy" to [Bennet McElwee][] for a fantastic plugin!


[TinyMCE]:        //www.tinymce.com "TinyMCE"
[Code Markup]:    //thunderguy.com/semicolon/wordpress/code-markup-wordpress-plugin/ "Code Markup Plug-in - Semicolon"
[Bennet McElwee]: //thunderguy.com/semicolon/ "Semicolon - by Bennett McElwee"
