---
layout: post
title: Running Out of Browsers...
author: Daniel
date: 2004-06-26 00:00:01
categories:
- [ Linux, My Linux Adventure ]
- [ Programming, PHP ]
summary: Browser lock-ups are leaving them in an unusable state
---

When I moved the mouse to try to get the screen to unblank this morning, nothing happened. It was locked up once again. I decided to only run one process, to see if I could isolate which one was causing me problems. I started with the F@H client. I started it before we left for breakfast, and when we got back, the computer was still running okay. I started using the computer actively, and found another problem - my profile for Firefox now thinks it's still in use, because I was using it when the machine crashed.

Now, anyone who has ever used Linux will know that one browser is much less severe than, for example, IE becoming unusable in a Windows evironment. I switched to using Mozilla, and was doing some research on Linux crashes when the machine locked up again. This time, when I restarted, both Firefox _and_ Mozilla thought they were still in use. I fired up the only other currently-installed browser, Konqueror, and surfed out to [LinuxQuestions.org][] and posted a message asking how to tell these browsers that they're not in use.

A few hours later, an answer appeared. For Firefox, the file is ~/.mozilla/firefox/default.lz7/lock, and for Mozilla, the file is ~/.mozilla/default/{something}.slt/lock. Both these are symbolic links to a process PID - deleting them freed up the default profiles so these browsers could be used again.

In the mean time, I have not restarted F@H, but I've had Evolution running in the background without incident. It seems that it may be the F@H client. That bugs me, because I was really looking forward to using this machine to help with the project. I may try to run the Windows version under [wine][], a Windows emulator for Linux.

Some folks have also expressed interest in the Tournament and Membership Tracking System (TMTS), which is a web application I coded to track membership and golf tournaments for a local golfing organization. They're interested in the PHP version, for which I no longer have the source code (it was on a laptop that was stolen). So, much of my computing effort over the next few days will be trying to get this recreated. Today, I was able to get the database rebuilt, and the first few pages converted.


[LinuxQuestions.org]: //www.linuxquestions.org
[wine]:               //www.winehq.com
