---
layout: post
title: "Success with Wine & Diagnostics"
author: Daniel
date: 2004-09-05 00:01:51
categories:
- [ Databases, MySQL ]
- Hardware
- [ Linux, My Linux Adventure ]
- [ Web Servers, Apache ]
summary: Some success and more troubleshooting
---

At work, we use an editor called [Visual SlickEdit][] (VSlick). It's got a lot of features, and supports color-coding for many different languages. I decided that I'd give wine another shot, as we only have the Windows version of this program. I installed wine and winesetuptk, used winesetuptk to configure the installation, then ran the installation program. Everything installed, and the program ran up to a point, when it started complaining about a missing DLL. I booted to WXP, found the DLL, copied it to the FAT32 drive, rebooted to Linux, and copied the DLL into the "fake windows" system directory. Soon, it was working great! I can't believe it - success with wine!

I also have made little headway towards getting Apache and MySQL to working. I changed the process that Apache uses to run as "summersd", and I was able to see pages (although any pages that relied on a database didn't work). I still haven't figured this one out yet...

I'm still getting kernel panics from time to time, and it seems to be whenever I access networking. A suggestion from one of the folks on the WBEL users list was to download the [Ultimate Boot CD][ubcd], filled with diagnostic programs. I downloaded it, burned it, and ran some memory checks. Those checked out, so I'm going to run a "CPU Burn-In" program to see if it can detect errors from the CPU. It runs for up to 7 days, but I think I'll just run it overnight - folding@home didn't take nearly that long to crash it before.


[Visual SlickEdit]: //www.slickedit.com
[ubcd]:             //www.ultimatebootcd.com
