---
layout: post
title: "A Tour of myPrayerJournal: Documentation"
date: 2018-09-01 12:37:00
author: Daniel
categories:
- [ Projects, myPrayerJournal ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- api
- composition
- configuration
- entity
- f#
- fish
- get
- giraffe
- handler
- json
- kestrel
- operator
- patch
- post
- rest
- router
- routing
- scott wlaschin
- spa
- suave
---
_NOTES:_
- _This is post 7 in a series; see [the introduction][intro] for all of them, and the requirements for which this software was built._
- _Links that start with the text "mpj:" are links to the 1.0.0 tag (1.0 release) of myPrayerJournal, unless otherwise noted._

We have spent a lot of time looking at various aspects of the application, written from the perspective of a software developer. However, there is another perspective to consider - that of a user. With a "minimalist" app, how do we let them know what the buttons do?

## Documentation via GitHub

In other projects, we had use [GitHub Pages][] by creating a `gh-pages` branch of our repository. This has some advantages, such as a page structure that is completely separate from the source files. At the same time, though, you either have to have 2 sandboxes on different branches, or switch branches when switching from coding to documentation. Fortunately, this is not the only option; GitHub Pages can publish from a `/docs` folder on the `master` branch as well. ([They even tell you how to set it up!][howto])


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[GitHub Pages]: https://pages.github.com
[howto]: https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/#publishing-your-github-pages-site-from-a-docs-folder-on-your-master-branch
