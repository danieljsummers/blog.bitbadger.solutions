---
layout: post
title: xine-lib 1.1.7 RPM
author: Daniel
date: 2007-08-21 21:10:15
categories:
- [ Hosted 64-bit Software, xine RPMs ]
tags:
- rpm
- xine-lib
---

Below are the library and development RPMs for xine-lib version 1.1.7. These have been built a little differently. First, these have been built on Ubuntu Linux, and converted to RPM using alien. Second, I could not quickly figure out how to get only the files tagged for the 1.1.7 release, so this is actually the in-work 1.1.8 release. I ran these builds through some paces, and nothing glaring came out. Be sure to check out the [About the xine RPMs][abt] post for more information.

xine-lib - The main xine library
xine-lib-dev - The development xine library (needed if you're building an interface against xine-lib)

You'll also need a user interface - as of this release, the most current release of [xine-ui is 0.99.5][ui].

(To save disk space, only the current release and two [prior releases][pri] will be maintained.)


[abt]: /2005/about-the-xine-rpms.html "About the xine RPMs &bull; The Bit Badger Blog"
[ui]:  /2007/xine-ui-0-99-5-rpm.html "xine-ui 0.99.5 RPM &bull; The Bit Badger Blog"
[pri]: /2005/xine-lib-1-1-1-rpm.html "xine-lib 1.1.1 RPM &bull; The Bit Badger Blog"
