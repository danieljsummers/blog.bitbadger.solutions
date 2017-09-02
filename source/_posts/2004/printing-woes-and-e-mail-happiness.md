---
layout: post
title: "Printing Woes & E-mail Happiness"
author: Daniel
date: 2004-06-29 00:00:42
categories:
- [ Linux, My Linux Adventure ]
summary: Printing is a challenge, but e-mail is working great
---

Printing is proving to be a challenge. Using [samba][] (the Linux [SMB][] libraries) and [cups][], I was able to configure my networked printer. However, I don't have a driver on my computer for it. I tried using some drivers that were close, but they didn't work - they just caused the printer to eject a blank page. I was creating the documents using [OpenOffice.org][]'s Writer, which has a "one-click PDF conversion" feature. When I went to use that feature, I found that I was on version 1.0, which didn't have the PDF stuff.

I downloaded the Linux install for version 1.1.2, and remembering that you need to use the "root" user to do most installs, did an [su][], and ran the install. I then launched the new writer and exported the PDF. Using samba, I copied the file over to the other computer, and was able to print it. However, since I installed it as root, it installed under /root, which meant that my normal user couldn't access it. At some point, I'll uninstall it and reinstall it in a public directory.

On another note, I posted a message about my inability to import mail from Netscape into Evolution to the WBEL user's list, and the response I got worked! Netscape actually stores the e-mail in the same format as many other Linux e-mail programs (a format called [mbox][] format). Using samba, I copied the files from "C:\Documents and Settings\Daniel\ApplicationData\Mozilla\Profiles\default\{somthing}.slt\Mail\Local Folders" - under this folder, there was a separate folder for each [POP3][] account, and within that folder, the file called "inbox" was my inbox. On some systems, the file is named "mbox", and it's in a folder with the name of the folder it represents (i.e., "Inbox/mbox"). Once these files were copied, I used Evolution's import utility - it prompted me for a file to import (whose type it determined automatically), and a location for the messages to go. I now have all my e-mail from my old setup!

One part of Unix/Linux of which I'm quickly becoming a fan is its adherance to the [Filesystem Hierarchy Standard][fhs] (FHS). The FHS defines where files should be found, and it represents all available files under a single directory, known as "/". No matter how many drives or network shares that are [mounted][], they're all under this directory. What this gives you is a system-wide view of your files, instead of the normal DOS-imposed separate drive specifications. The FHS also says what files are supposed to be in what directories, so no matter what Unix/Linux system you're using, once you know the FHS, you know exactly where to look for things.


[samba]:          //us1.samba.org/samba/samba.html
[SMB]:            //www.computerhope.com/jargon/s/smb.htm
[cups]:           //www.cups.org
[OpenOffice.org]: //www.openoffice.org
[su]:             //hegel.ittc.ukans.edu/topics/linux/man-pages/man1/su.1.html
[mbox]:           //email.about.com/cs/standards/a/mbox_format.htm
[POP3]:           //www.computerhope.com/jargon/p/pop.htm
[fhs]:            //www.pathname.com/fhs/
[mounted]:        //www.computerhope.com/jargon/m/mount.htm
