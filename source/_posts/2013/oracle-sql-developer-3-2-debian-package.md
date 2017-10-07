---
layout: post
title: Oracle SQL Developer 3.2 Debian Package
author: Daniel
date: 2013-06-22 23:24:04
categories:
- [ Databases, Oracle ]
- [ Programming, SQL ]
tags:
- deb
- java
- oracle
- sql developer
summary: A new version of Oracle's SQL Developer
---

Oracle has released version 3.2 (.20.09) of their SQL Developer tool. They're still releasing RPMs, so developers on Debian-based systems need to use alien to install it on their machines. We have done that, and have made this available for others to use as well. What makes this particular release of SQL Developer so great is that [it now runs reliably under Java 1.7][java7] - no more keeping a 1.6 JDK floating around just for SQL Developer!

The .deb package [can be downloaded here][deb], or you can browse current and previously posted packages [in the "SQL Developer" directory][dir] of the Bit Badger Solutions Software Repository.


[java7]: //www.thatjeffsmith.com/archive/2013/06/oracle-sql-developer-and-java-7/ "Oracle SQL Developer and Java 7 &bull; That Jeff Smith"
[deb]:   //hosted.djs-consulting.com/software/sqldeveloper/sqldeveloper_3.2.20.09.87-2_all.deb "SQL Developer 3.2.20.09 Debian Package"
[dir]:   //hosted.djs-consulting.com/software/sqldeveloper/ "SQL Developer &bull; Bit Badger Solutions Linux Software Repository"
