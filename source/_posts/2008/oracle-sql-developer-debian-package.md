---
layout: post
title: Oracle SQL Developer Debian Package
author: Daniel
date: 2008-10-29 07:17:14
categories:
- [ Databases, MySQL ]
- [ Databases, Oracle ]
- [ Databases, PostgreSQL ]
- [ Databases, SQL Server ]
- [ Programming, SQL ]
tags:
- alien
- deb
- java
- oracle
- rpm
- sql developer
summary: A .deb package for Oracle's SQL Developer product
---

[Oracle SQL Developer][sd] is a Java-based tool that provides a graphical interface to a database. While it's main focus is Oracle (of course), it can be hooked up, via JDBC, to many other databases, such as MySQL, PostgreSQL, and SQL Server. It's similar to [Toad][], but is provided by Oracle at no cost.

Oracle provides SQL Developer in either an RPM, or a generic binary install. I like the ability to manage packages, but I've never had much luck at getting RPM to run on Ubuntu. I downloaded the RPM file, and, using [alien][], I converted the package to a .deb package (Debian package format) and installed it. It worked like a charm!

I haven't tested it with gcj, but using Sun's Java 6 update 7 from the Ubuntu repositories, it ran just fine. After you install the package, do a directory list on `/usr/lib/jvm`. You're looking for the Sun JDK - if it's installed, you'll have a symlink java-6-sun that points to java-6-sun-1.6.0.07. Once you've determined the location of the JDK, run "sqldeveloper" from the command line - the program will prompt you for the path to your JDK. Enter it (probably `/usr/lib/jvm/java-6-sun`) and you're good to go. (You have to install the package as root - but, for the rest of these steps, use your normal user, not root, as this puts settings in a .sqldeveloper directory off your home directory.) The package installs an icon in the "Programming" or "Development" group. Once you've told it where the JDK is, you can use this to launch it.

[Download SQL Developer 1.5.1 Debian Package][deb]


[sd]:    //www.oracle.com/technology/products/database/sql_developer/index.html "Oracle SQL Developer &bull; Oracle"
[Toad]:  //www.toadsoft.com
[alien]: //kitenet.net/~joey/code/alien/
[deb]:   //djs-consulting.com/linux/software/sqldeveloper/sqldeveloper_1.5.54.40-2_all.deb "SQL Developer 1.5.1 Debian Package &bull; Bit Badger Solutions Linux Software Repository"
