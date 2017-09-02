---
layout: post
title: HCSB Verse of the Day 1.0 - WordPress Plug-In
author: Daniel
date: 2007-09-25 22:08:24
categories:
- [ Programming, PHP, WordPress ]
tags:
- bible gateway
- hcsb
- plug-in
- wordpress
summary: A WordPress plug-in to display a verse or passage each day
---

I have used a hack in the theme of my personal site for a while to obtain the "Verse of the Day" in the Holman Christian Standard Bible (HCSB) version. With things that I've learned about plug-ins, I decided to take a stab at creating a plug-in to do this work. That way, if I changed themes, I wouldn't have to hack the new one they way I've hacked the current one.

The fruits of my labor is the new HCSB Verse of the Day plug-in for WordPress. It uses the reference from [BibleGateway.com][], then gets the text from their regular website. (They do not provide the service, citing copyright restrictions.) How to use the plug-in, along with my justification on copyright grounds, is in the top of the file's comments. If they're not clear, certainly post comments here and I'll address them. (I do plan to submit the plug-in to [WordPress.org][] - but, they require a specific README file format that it will take me a bit to put together.)

<del>votd\_hcsb.txt - HCSB Verse of the Day 1.0</del> _(UPDATE: It is in the plugin directory now.)_

<del>To install it, download the file, rename it "votd_hcsb.php", and upload it to your /wp-content/plugins directory. Then, enable it, and add the template tags to your theme - that's it!</del>


[BibleGateway.com]: //www.biblegateway.com "Bible Gateway"
[WordPress.org]:    //wordpress.org "WordPress"
