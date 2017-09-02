---
layout: post
title: Webshots - Wine Strikes Again!
author: Daniel
date: 2004-09-10 00:00:29
categories:
- [ Linux, My Linux Adventure ]
summary: Using wine to use Webshots
---

When I ran Windows as my desktop, I had a program called [WebShots][] that I used to set my desktop wallpaper, and cycle it. They have Windows and Mac versions, but no Linux version yet. They still send me e-mails each week, showing the daily picture selections for each day in the past week. I decided to download the Windows version, and install in under wine to see if it would work. I moved `websamp.exe` to `/home/summersd/.wine/fake_windows`, then ran `wine C:\\websamp.exe` to install the program. `wine "C:\\Program Files\\Webshots\\Launcher.exe"` then started the desktop control. I used that to disable the tray icon (wine has one, but you can't see it), and I disabled almost every other "auto update" feature.

I had downloaded a ".wbz" file (which is what is imported into WebShots), and I finally figured out how to import it. Running the launcher program, and following it with the name of the .wbz file, imports it. I may figure out a way to automate that, but for now, I know how to do it.

_(Note: This is the end of the "My Linux Adventure" series of posts. After this, I ended up going back to Windows XP, just because it worked and I didn't have hobbyist time. As of May 2007, I'm running Ubuntu 7.04 on one computer, and Windows Vista on my laptop, which is currently out of commission.)_


[WebShots]: //www.webshots.com
