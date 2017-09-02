---
layout: post
title: Daniel's DropDowns 2 - WordPress Plug-In
author: Daniel
date: 2007-12-23 23:02:49
categories:
- [ Programming, PHP, WordPress ]
tags:
- category
- dropdown
- plug-in
- update
- wordpress
summary: A new version of the category / archive plug-in
---

I have released version 2 of my category and archive drop-down plug-in for [WordPress][]. It is hosted at the WordPress Plug-In Directory - you can [go get it there][pi]. Following are a few of the changes that were made.

* Both tags now have only 2 parameters - the type of navigation and the text for the link or button. For navigation type, 'button' remains the default, and 'link' is still available. However, a third option of 'auto' has been added, which will render the list as an auto-navigating select box.
* The CSS parameters for the select and button elements were dropped. How these elements can be specified using CSS is detail in the top-of-plugin comments, along with an example.
* The usage examples in the comments now have an example of how to put the tag in a template in such a way that, if the plug-in is disabled, the template will still render. This could be done with 1.0, but I didn't give an example.
* I added PHP Documentor-style comments to both functions.

Let me know if you have any problems with it, or any ideas for other behavior. Enjoy!

_(UPDATE: This plug-in is inactive, as its functionality is now part of WordPress core.)_


[WordPress]: //wordpress.org "WordPress"
[pi]:        //wordpress.org/extend/plugins/daniels-dropdowns/ "Download Daniel's DropDowns 2"
