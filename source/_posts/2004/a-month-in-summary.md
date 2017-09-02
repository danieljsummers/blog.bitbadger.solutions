---
layout: post
title: A Month in Summary
author: Daniel
date: 2004-08-31 00:00:12
categories:
- [ Databases, MySQL ]
- [ Linux, My Linux Adventure ]
- [ Web Servers, Apache ]
- [ Web Servers, IIS ]
summary: Progress over the past month
---

Well, the last month has been interesting. I was able to get my Windows and Linux installations synchronized by creating a mount point for my second drive under <tt>/mnt/drive\_d</tt>. Under that, I created a directory called /thunderbird for my e-mail, and moved my e-mail and newsgroup folders over there. (The first time, I missed the "newsrc" file, which is important - it tells what newsgroups you've subscribed to and which messages you've read.) Under Windows, I pointed it to <tt>D:\thunderbird\pop3.knology.net</tt>, and under Linux, it was configured to <tt>/mnt/drive\_d/thunderbird/pop3.knology.net</tt>. I then moved the <tt>wwwroot</tt> directory from <tt>C:\Inetpub</tt> to drive D:, and pointed IIS to the new location. Under Linux, I did something a little different. As "root", I deleted the directory <tt>/var/www/html</tt>, and instead created <tt>/var/www/html</tt> as a symbolic link to <tt>/mnt/drive\_d/wwwroot</tt> (the actual command is <tt>ln -s /mnt/drive\_d/wwwroot /var/www/html</tt>). That worked great as well.

MySQL was more complicated, but I was eventually able to get it working as well. I created the directory <tt>D:\mysql\data</tt> for the data, then configured <tt>/etc/my.cnf</tt> under Linux to look at <tt>/mnt/drive\_d/mysql/data</tt>. I kept getting "Could not connect to server using socket /var/lib/mysql/mysql.sock". After some digging, it appeared to be a permissions problem. All the documentation said that the default socket was /tmp/mysql.sock, so I changed my.cnf to point there instead, restarted mysqld, and it worked! So, I have no idea what a Unix socket it, but I know that now I have one! :)

I was also able to get DVDs playing using xine, compiling it myself, and using [libdvdcss][], I can even watch commercial DVDs. I'm really impressed with xine - it handles all kinds of media out of the box, including DivX and up to version 8 of WMV files. You can add codecs to it as well, to support almost anything you want to do from an audio or video perspective. Compiling the player took around 20 minutes, and compiling the front end took another 5. And, it was simple - download the .tar.gz file, do <tt>tar xvfz [name].tar.gz</tt>, <tt>cd [name]</tt>, <tt>./configure</tt>, <tt>make install</tt>. The <tt>./configure</tt> script is the key in the whole process - it looks at what you have installed, and creates make files that will work with your compiler.

Everything started going south, though, when I started having freezes. Eventually, I got to where I could not boot without a kernel panic, and then boot errors (which I detailed in [this e-mail to the WBEL user's list][email]. Encouraged by my success over the past month, I decided to return to WBEL - it's supposed to be more stable than FC2, and I bet that I can get ndiswrapper, the dual-booting web server, the common e-mail, and maybe even some other stuff working again.


[libdvdcss]: //developers.videolan.org/libdvdcss/
[email]:     //beau.org/pipermail/whitebox-users/2004-August/003197.html
