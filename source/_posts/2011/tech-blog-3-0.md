---
layout: post
title: 'Tech Blog 3.0 (aka "You win, PHP...")'
author: Daniel
date: 2011-08-24 22:12:44
categories:
- General Info
- [ Programming, .NET, Mono ]
- [ Programming, PHP ]
- [ Web Servers, Apache ]
tags:
- apache
- blog
- blogengine
- mpm
- php
- pre-fork
- theme
- thread
- wordpress
- worker
summary: This site has returned to WordPress
---

After a little over a year running on [Tech Blog 2.0][], you are now viewing version 3.0. For this version, we've returned to [WordPress][] from [BlogEngine][]. There are several issues that colluded to drive this change, most of which surrounded PHP and its crazy behavior. (Geeky details follow - skip to the paragraph starting with "Bottom line:" if you don't want the geek stuff. I bolded it so it would be easy to spot.)

PHP's recommended configuration is to run under Apache using the pre-fork multi-processing module (MPM). The advantage to this is that Apache does not have to spin off another process to handle each request; it handles it in the same thread. However, this means that each instance of the server must have all enabled modules loaded. This means that each instance of the server (AKA "thread") is very large, so the number of threads run is lower (typically 5-15 in a server the size we're on). Also, this means that each thread can only handle one request at a time; if you have 7 threads configured, each serving one of 7 requests, and an 8th request comes it, it has to wait for one to finish. If the requests are served quickly, this may not be a problem; however, the avalanche of request that follow the typical front-page mention on mega-blogs can easily overwhelm it.

To fix this problem, there is another MPM, this one called worker. In this scenario, there are spare thread waiting to fill requests, and these can spawn other threads to do further work if required. So, the Apache threads would realize that a request needs to be handled by PHP, and pass it off to that process to be completed. The Apache memory footprint is much smaller; it serves the images, scripts, and other static files, and passes off the requests that require heavy lifting. PHP, then, has a (FastCGI) process where it receives these requests, processes them, and returns the response to the caller. Because each of these threads only has to load the PHP requirements, they are smaller too, so you can have more threads processing at the same time; you just might survive that front-page mention! (This is the same technique applied by LightTPD and Nginx, two other servers I tried at various times.)

It is in this scenario where PHP fails to live up to its expectations. These PHP processes would simply stop responding, but the controller thinks they're still there. The end result to the user is a site that just sits and waits for output that will never come. Eventually, they may receive a Gateway Timeout or Bad Gateway error. The problem is worse on slower sites, but even popular sites seemed to fall victim to this from time to time. This was also a problem whether PHP controlled its threads, or Apache controlled them.

The one thing that really perturbs me is instability. If something is broken, I can fix it; if it works, I can fix it 'til it's broke. :) But something that works sometimes, and other times doesn't, simply won't fly. I was able to introduce some stability by restarting the server 4 times a day, but that's a band-aid, not a long term solution. I was tired of fighting.

**Bottom line**: the configuration required for a stable server is in opposition to a lean-and-mean configuration. So, I installed the required Apache modules, and will continue to run my PHP-serving server at a configuration twice as large as it needs to be. I'll eventually move the Mono (.NET) processes to another machine, where the fast configuration won't cause stability problems.

But, PHP isn't all. While I would still heartily recommend BlogEngine.NET to someone who was going to serve the blog from a Windows machine, but I had some issues getting upgrades to go smoothly under Mono. It also is optimized for fast serving, at the expense of RAM. At this point, that's not the tradeoff we need.

Finally, with this update, the blog has received its first new theme. It's a clean, clear theme that should serve the content well. Plus, the social media icons up in the corner are just too cool, IMO. I've also applied tags to all posts except the "My Linux Adventure" series, and this theme displays them. (Comments are not here now, but will be migrated shortly.)

So, there you have it. Enjoy!


[Tech Blog 2.0]: /2010/tech-blog-2-0.html "Tech Blog 2.0 &bull; DJS Consulting Tech Blog"
[WordPress]:     //wordpress.org "WordPress"
[BlogEngine]:    http://dnbe.net/docs/ "BlogEngine.NET"
