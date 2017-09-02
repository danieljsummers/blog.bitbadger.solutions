---
layout: post
title: Wow
author: Daniel
date: 2004-08-01 00:00:34
categories:
- [ Databases, MySQL ]
- [ Linux, My Linux Adventure ]
- [ Programming, PHP ]
- [ Web Servers, Apache ]
- [ Web Servers, IIS ]
summary: Fedora Core 2 makes a nice first impression
---

Today I installed Fedora Core 2. This thing is slick! WBEL looked a lot like RH8, which I had seen before my renewed Linux learning began. FC2 has a graphical loader that hides a lot of the background stuff (unless an error occurs) - that's cool. During the install, I skipped OpenOffice.org and MySQL, although I installed PHP with MySQL support. The reason for that is that I wanted to get the latest and greatest versions of those two products. We'll see if this proves to be a good decision or not.

The wireless network card still wasn't recognized (phooey). I did some more searching, armed with the knowledge that I have an adm8211 chipset. One of the first hits under Google's Linux search for "adm8211" pointed me to a project called [NDISwrapper][]. This is a "wrapper" that uses the vendor's Windows DLL file, and converts the hooks from Windows to Linux. Doing this, this driver can (in theory) support most any network card, especially those that aren't in the Linux Hardware Compatibility List (HCL). I downloaded it, compiled it, and followed the directions to install my driver under it. I still wasn't able to create a connection, but on a hunch, I restarted the computer. NDISwrapper is also a kernel module, and I know that often those are only read at startup. Once the computer was restarted, I was able to create a connection, and now my network card works! YEA!!! (And it was only one night's worth of work - much better.)

Now that I have networking working under both operating systems, I plan to try to get four things working the same, whether I'm booted to WXP or Linux - E-mail (using Mozilla Thunderbird), PHP (using Apache on Linux, IIS on WXP), MySQL (using the exact same version on both), and a web server that uses the same html root directory (again, Apache on Linux, IIS on WXP). If I didn't already have IIS up, running, and configured under WXP, I'd probably just do Apache on both, but this will be interesting - it should work, as I don't have many creative permission rules.


[NDISwrapper]: //ndiswrapper.sourceforge.net
