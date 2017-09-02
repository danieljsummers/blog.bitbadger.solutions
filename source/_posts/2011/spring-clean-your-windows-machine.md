---
layout: post
title: Spring Clean Your Windows Machine
author: Daniel
date: 2011-05-07 15:48:26
categories:
- Security and Privacy
tags:
- compcln
- security
- spring
- vista
- windows
- winsxs
summary: Reclaim significant space on your Windows drive by deleting old versions of system libraries
---

Spring has sprung, the grass has riz; do you know where your HD space is? If you're running Windows Vista or Windows 7, you may have some unclaimed disk space waiting for you. (Disclaimer: I am not telling you to do anything to your computer, and I maintain no liability for the effects of the commands you enter. I'm sharing what worked for me.)

Windows Vista was a rewrite of Windows; as part of this, they developed Windows Side by Side (WinSxS) to deal with the conflicting/removed DLL issue that plagued Windows in the past. SxS maintains components, and programs continue to use their components unless they specifically ask to use a new one. This keeps upgrades from breaking older programs, and makes all upgrades reversible. With Vista currently at SP2, you probably have lots of versions of several of these components, and if you have a smaller drive, they can be pinching your disk space. If you're content with the way your computer is running, SP2 includes a utility called COMPCLN.EXE which will make these upgrades permanent by removing unused components. WinSxS knows which components are referenced by current software, so you can run this without worrying that you'll break an older program.

To run it, click the Windows icon on the bottom left of the task bar, type "cmd", then press Enter. When the command prompt window opens, type "compcln" and press Enter. It will give you a y/n prompt, then clean the old components off your computer. Windows also makes restore points, which is a saved group of files and settings that exist before installing updates. If you're cleaning the components, you can also delete these as well. To run this, open the control panel and search for "disk cleanup". Choose your C: drive, then click the "More Files" tab. The restore point button is in the middle of that page.

Finally, Microsoft has released [Microsoft Security Essentials][mse], an anti-virus/anti-malware program for Windows XP through 7. If you're tired of "buy the real version" nags or renewing subscriptions, this is the tool for you. It's a tool that many feel should have been included in Windows for a long time (though the reasons why it hasn't been are outside the scope of this how-to), it works well, and it's free.


[mse]: //www.microsoft.com/security/pc-security/mse.aspx "Microsoft Security Essentials"
