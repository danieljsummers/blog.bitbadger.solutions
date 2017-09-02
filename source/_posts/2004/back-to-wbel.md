---
layout: post
title: Back to WBEL
author: Daniel
date: 2004-09-04 00:00:02
categories:
- [ Databases, MySQL ]
- [ Linux, My Linux Adventure ]
summary: WBEL has been reinstalled
---

Today, I reinstalled WBEL 3.0. I was able to compile ndiswrapper (as I kept that on my FAT32 drive), and get the network card working smoothly very quickly. (In fact, it seems to be more reliable under Linux than WXP!) With the network up, it was easy to download Firefox, Thunderbird, and OpenOffice, and installing them was a breeze. (I decided to put them under /opt this time, trying to stick with the FHS.) I decided to mount my FAT32 drive under my home directory, as `/home/summersd/drive_d`. E-mail works fine, but Apache gives me a 403 (Permission Denied) error. MySQL doesn't seem to be working either - I'll have to play with that later.
