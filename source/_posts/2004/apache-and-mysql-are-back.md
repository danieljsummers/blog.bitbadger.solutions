---
layout: post
title: Apache and MySQL Are Back
author: Daniel
date: 2004-09-08 00:01:50
categories:
- [ Databases, MySQL ]
- [ Linux, My Linux Adventure ]
- [ Web Servers, Apache ]
summary: Apache and MySQL work on both sides of the dual-booted computer now
---

I was finally able to resolve my problems with Apache and MySQL. When I decided to mount my FAT32 drive under <tt>/home/summersd</tt>, I inadvertently caused myself some problems. From talking to a Linux guy at work, I found that no processes that weren't running under my user ID could access those files. The reason is that Linux looks up the entire diretory tree, back to /, to determine if you can access the file. So, although I had <tt>-rwxrwxrwx summersd summersd</tt> on every file, <tt>/home/summersd</tt> was <tt>drwx------ summersd summersd</tt>, and <tt>/home</tt> was <tt>drwxr-xr-x</tt>. The permissions on <tt>/home/summersd</tt> was keeping Apache from seeing <tt>/home/summersd/drive\_d/wwwroot</tt>, and MySQL from seeing or writing to <tt>/home/summersd/drive\_d/mysql/data</tt>. I moved the drive to <tt>/mnt/drive\_d</tt>, with the mount point being owned by "root", still mounting the drive with my user name, and everything worked.

In the process of reconfiguring Thunderbird, I believe I may have found out how to share the address book across operating systems. The file <tt>~/.thunderbird/default.[something]/prefs.js</tt> has a listing of all the preferences and settings. I modified this file to change the location of my mail files, and there is a setting there for an address book (which isn't shown in the configuration dialog - after all, it is 0.7.3...) I'll play with that later - right now I'm just elated to have Apache and MySQL working again.
