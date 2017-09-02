---
layout: post
title: HCSB Verse of the Day 2 - WordPress Plug-In
author: Daniel
date: 2008-01-01 11:02:11
categories:
- [ Programming, PHP, WordPress ]
tags:
- bible gateway
- hcsb
- plug-in
- update
- wordpress
summary: Significant updates to the verse-of-the-day plug-in
---

I have released version 2 of HCSB Verse of the Day, the [WordPress][] plug-in that provides a verse or passage each day, using the reference provided by [BibleGateway.com][]. I also completed the required files for the WordPress Plug-In Directory, so it can be [downloaded from there][pi].

New in this version...

* **New Tag** - There is now a tag votd_hcsb() that puts out the heading, the text, the reference, and the credit line all in one. This will simplify the template modification required to implement the plug-in.
* **Custom Tag** - There is a separate file where you can specify a separate group of tags, and the votd_hcsb() tag will utilize it instead of its default. This also means that, even if future versions change the default, the custom tag layout will be used.
* **Two Versions** - WordPress (and most plug-ins) must be compatible with PHP version 4. However, if your web server is running PHP version 5, there is now a PHP 5 version included. It incorporates the object-oriented enhancements in PHP 5.
* **Options Revamped** - Since I initially wrote the plug-in, I've learned that WordPress allows an option to be an array. So, to streamline its usage, the options are now an array, and only require one row in the database instead of five. There is also a file to clean up the old options.
* **Bug Fixes** - BibleGateway.com changed the way they display multiple passages (ex. "Matthew 1:13, 17-19"); version 2 has a fix that makes that work again.

As always, if you encounter any problems with the plug-in, just let me know and I'll try to help. Enjoy!


[WordPress]:        //wordpress.org "WordPress"
[BibleGateway.com]: //www.biblegateway.com "Bible Gateway"
[pi]:               //wordpress.org/extend/plugins/hcsb-verse-of-the-day/ "Download HCSB Verse of the Day 2"
