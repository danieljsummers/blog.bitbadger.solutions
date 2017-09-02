---
layout: post
title: Tech Blog 2.0
author: Daniel
date: 2010-08-05 19:09:54
categories:
- General Info
- [ Programming, .NET, C# ]
tags:
- .net
- blogengine
- fastcgi
- mono
- php
- wordpress
---

After three years on [WordPress][], the DJS Consulting Tech Blog has moved to [BlogEngine.NET][]. There are several reasons for this change, some technical and some not.

* PHP's Fast CGI processor has a problem where, if all of the processes are busy, the server will simply time out. While this hasn't afflicted my server as much as others, it has caused problems; when this problem occurred, none of the PHP sites were accessible.
* Through experience with a very heavily-used site, I became less enamored of WordPress's "read from the database every time" way of doing business. I also found that various caching plug-ins for WordPress, on this particular site, did very little to ease the load.
* Since I first looked at Mono (Linux's implementation of the .NET framework), it has matured significantly. It supports most of C# 4.0 already, which was released earlier this year.
* BlogEngine.NET is a rapidly-maturing blog platform, and the project has a stated goal of 100% compatibility with Mono. This is good, because you can mention Mono problems to the team, and you're not dismissed because you're running Linux.

As part of the move, the URL has changed; the new link is <https://techblog.djs-consulting.com>. I have implemented redirection for each post, the category and category feed links, and the main blog feed and home page from the old URL, so you may not have even realized that you're looking at the new site. The DJS Consulting Software Repository remains at <https://hosted.djs-consulting.com/software>.

I'm looking forward to this new setup!

_(NOTE: The next-to-last paragraph was updated with correct links as of February 2017.)_


[WordPress]:      //wordpress.org "WordPress"
[BlogEngine.NET]: //dotnetblogengine.net "BlogEngine.NET"
