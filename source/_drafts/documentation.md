---
layout: post
title: "A Tour of myPrayerJournal: Documentation"
date: 2018-09-01 12:37:00
author: Daniel
categories:
- [ Projects, myPrayerJournal ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- documentation
- github
- https
- jekyll
- lets encrypt
- liquid
- markdown
- subdomain
---
_NOTES:_
- _This is post 7 in a series; see [the introduction][intro] for all of them, and the requirements for which this software was built._
- _Links that start with the text "mpj:" are links to the 1.0.0 tag (1.0 release) of myPrayerJournal, unless otherwise noted._

We have spent a lot of time looking at various aspects of the application, written from the perspective of a software developer. However, there is another perspective to consider - that of a user. With a "minimalist" app, how do we let them know what the buttons do?

## Setting Up GitHub Pages

In other projects, we had use [GitHub Pages][] by creating a `gh-pages` branch of our repository. This has some advantages, such as a page structure that is completely separate from the source files. At the same time, though, you either have to have 2 sandboxes on different branches, or switch branches when switching from coding to documentation. Fortunately, this is not the only option; GitHub Pages can publish from a `/docs` folder on the `master` branch as well. They have a [nice guide on how to set it up][howto].

The `_config.yml` file that's also in the `/docs` folder ([mpj:docs folder][docs]) was put there by GitHub when we selected the theme from the Settings page of the repo. The only file we ever put there was `index.md` ([mpj:docs/index.md][index.md]).

## Writing the Documentation

As GitHub Pages uses [Jekyll][] behind the scenes, we have [Markdown][] and the default [Liquid][] tags available to us. We didn't need any Liquid tags, though, as the documentation is pretty straightforward. You may notice that there isn't any front matter at the top of `index.md`; absent that, GitHub Pages selects the title of the page from the first `h1` tag in the document, defined with a leading `# ` sequence.

If you browse the [commit history for `index.md`][commits], you'll see that many of the commits reference either a version or issue number. This makes it rather simple to go back in time through the source code, and not only review the code, but see how its functionality was explained in the documentation. You can also review typos that got committed, which helps keep you humble. :)

## Making It Better

There is more that could be done to improve this aspect of the project. The first would be to assign it a subdomain of `prayerjournal.me` instead of serving the pages from `bit-badger.github.io`. GitHub Pages does support custom subdomains, and even supports HTTPS for these through [Let's Encrypt][le]. The second would be to work up a lightweight theme for the page that matched the look-and-feel of the main site; this would make a more unified experience. Finally, while "minimalist" was the goal, there ended up being a few features that took lots of words to explain; a table of contents on the page would help people navigate directly to the help they need.

<p>&nbsp;</p>

Our tour is drawing to a close; next time, we'll wrap it up with observations and opinions over the development process.


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[GitHub Pages]: https://pages.github.com "GitHub Pages"
[howto]: https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/#publishing-your-github-pages-site-from-a-docs-folder-on-your-master-branch
[docs]: https://github.com/bit-badger/myPrayerJournal/tree/1.0.0/docs "docs | myPrayerJournal | GitHub"
[index.md]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/docs/index.md "docs/index.md | myPrayerJournal | GitHub"
[Jekyll]: https://jekyllrb.com "Jekyll"
[Markdown]: https://daringfireball.net/projects/markdown/ "Markdown"
[Liquid]: https://github.com/Shopify/liquid/wiki "Liquid"
[commits]: https://github.com/bit-badger/myPrayerJournal/commits/1.0.0/docs/index.md "docs/index.md (Commits) | myPrayerJournal | GitHub"
[le]: https://letsencrypt.org "Let's Encrypt"
