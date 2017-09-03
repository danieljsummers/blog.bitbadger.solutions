---
layout: post
title: Transferring Data Between Oracle and SQL Server
author: Daniel
date: 2007-07-09 15:23:10
date_gmt: '2007-07-09 21:23:10 +0000'
categories:
- [ Databases, Oracle ]
- [ Databases, SQL Server ]
tags:
- data
- dts
- instant client
- oracle
- sql server
summary: Using Oracle's Instant Client for easy SQL Server data exchange
---

There are lots of "how to" articles on sharing data between Oracle and SQL Server. Most of these involve installing Oracle's code base on the SQL Server machine, then using that instance to link tables within Oracle. This technique does not require that, thanks to a product from Oracle called [Oracle Instant Client][ic].

To set up the Oracle piece, download the packages for "Basic" and "ODBC Supplement", and follow the instructions for installation, on the machine with SQL Server. (This is not an "install" per se - it's basically an unzip.) Next, you'll need to provide a TNSNAMES.ORA file - this can be any valid file, including a simple shell with an "ifile=" statement pointing to a common TNSNAMES.ORA file. Finally, set the environment variable TNS_ADMIN to point to the directory where this TNSNAMES.ORA file resides.

Now, you can easily create a DTS script through SQL Server to push or pull data however you'd like. Oracle Instant Client will appear in the drop-down list of providers, and you'll be able to specify your connection the way you normally do (i.e., "DB01.WORLD").

Happy migrating!


[ic]: http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html "Download Oracle Instant Client"
