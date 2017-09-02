---
layout: post
title: "Sour Milk Is Right!"
author: Daniel
date: 2004-07-08 00:00:09
categories:
- [ Linux, My Linux Adventure ]
summary: A lead on a wireless driver doesn't work on my kernel
---

A user wrote back and suggested that I try a driver from [SourMilk.net][], which is an "adm8211" driver project for Linux. When I downloaded it and started to compile it, I quickly became aware that it hoped I was on the 2.6 family of kernels (which is what the Fedora Project, and several of the newer distributions, are now using). WBEL 3.0 uses the 2.4 kernel, which is more established, but is beginning to drop off as most folks start developing for 2.6. Anyway, there was a 2.4 make file, but it was labeled "experimental."

That pretty much describes how I spent my time this evening - experimenting. I have virtually no experience with large-scale C projects, and I got many, many compile errors. I tried tweaking the make file, to no avail. I don't fault the driver, as it said up front that it is really targeted to 2.6. I may end up downloading a 2.6 distribution and trying it out.


[SourMilk.net]: //aluminum.sourmilk.net/adm8211/
