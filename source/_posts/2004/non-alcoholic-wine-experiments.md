---
layout: post
title: Non-Alcoholic Wine Experiments
author: Daniel
date: 2004-07-02 00:00:27
categories:
- [ Linux, My Linux Adventure ]
summary: First efforts with wine leave me unimpressed
---

Wine is not getting me drunk. I installed it, downloaded and installed [WineSetupTK][] to assist with configuration. I then ran the setup program (through wine) for _Thomas the Tank Engine: The Great Festival Adventure_. Once that completed, I tried to run the program (by entering `wine "C:\Program Files\Hasbro Interactive\The Great Festival Adventure\thomas.exe"`) and got a message box saying "CD check path not found." I searched the web for this message, and didn't find anything - it may be a message from the game, and not wine. I'll do some digging later.

You may remember the problems I had with the Folding@Home client. So, I decided to try to run the Windows version through wine. I downloaded it, and ran it through wine. It took about 2 minutes to lock my machine up. I'm not being too hard on wine for this stuff - an emulator isn't going to be 100%. I am concerned that I don't seem to be able to run any sort of Folding@Home client, and since it's not open-source, I can't try compiling on this computer.

I uninstalled the OpenOffice.org suite from the /root directory, and installed it in `/usr/local/OpenOffice.org1.1.2` (which was where it suggested). Now, it works for normal users. I still haven't been able to resolve my printing problems, but I sent a question today to the WBEL users list - they came through quickly for helping me resolve my mail importing issues.


[WineSetupTK]: //sourceforge.net/project/showfiles.php?group_id=6241&amp;package_id=77479&amp;release_id=161192
