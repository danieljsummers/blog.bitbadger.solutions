---
layout: post
title: Tech Blog v4
author: Daniel
date: 2017-02-16 19:21:00
categories:
- General Info
- [ Web Servers, nginx ]
tags:
- blog
- jekyll
- nginx
- wordpress
summary: The blog moves from WordPress to Jekyll
---

From [August 2011][v3] until today, this site has been running under WordPress. During this time, we have done many experiments with several other blog platforms, but none of them made it to the "import all the old stuff from this one - this is what we're looking for!" stage. As you may have already guessed, though, this is no longer the case.  WordPress does what it does very well. However, the last post before this one was August... of 2014! That means that, every page served from this site, a script was run on the server that accessed a database and dynamically assembled the page. This content did not need to be dynamic - even when there is a new post, there is very little in the way of dynamic content here.

[Jekyll][] is a static site generator; these types of applications generate static sites based on a collection of templates and content files, that can then be served with no backing data store. Now, we can utilize the blazing fast [nginx][] web server to push these files as quick as people can request them, where the request does not even have to escape the process.

There will be more to come on Jekyll; there are at least two posts to be written, one on automating the build process and another on the migration from WordPress. Until then, though, there are redirects that ensure the RSS feeds for both the main blog and the xine RPMs require no changes, and the category pages have redirects as well. If something does not look right, let us know via either of the social media accounts linked above.


[v3]:     /2011/tech-blog-3-0.html "Tech Blog 3.0 &bull; DJS Consulting Tech Blog"
[Jekyll]: //jekyllrb.com "Jekyll"
[nginx]:  //nginx.com "nginx"
