---
layout: post
title: ndiswrapper May Have Issues
author: Daniel
date: 2004-09-07 00:00:49
categories:
- Hardware
- [ Linux, My Linux Adventure ]
summary: Wireless drivers don't handle the lack of a wireless network very gracefully
---

We're speedily working towards a Friday deadline at work, so tonight I had some analysis work to do on some COBOL code. Great, I thought, I can use my VSlick setup under wine. I moved my computer from the living room back to our now-empty bedroom (soon to be nursery), and booted it up. Kernel panics galore - never got past the network stuff. When I booted to Windows, I found that the wireless network didn't reach that far, and I'm guessing that the ndiswrapper folks haven't tried their driver a lot with a wireless card, but no wireless network. Once I get past that, I may grab the dumps from these kernel panics and see if the developers need them to see what went wrong. So, for tonight, I had to use WXP (in which I actually had to disable the wireless connection - seems Windows doesn't handle a barely-there wireless connection much better than Linux).

The diagnostics I ran last night never found anything - they ran for about 10 hours. I suppose I'll just have to wait until I have problems again, then run it right then. Another person from the WBEL users list suggested I check the way I have my hard drives set up; he thinks that a 2GB drive slaved to a 20GB drive may be causing conflicts, which would cause freezes or panics.
