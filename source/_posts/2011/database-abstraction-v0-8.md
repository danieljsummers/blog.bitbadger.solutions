---
layout: post
title: Database Abstraction v0.8
author: Daniel
date: 2011-10-22 21:00:48
categories:
- [ Databases, MySQL ]
- [ Databases, PostgreSQL ]
- [ Databases, SQL Server ]
- [ Databases, SQLite ]
- [ Programming, .NET, C# ]
- [ Projects, Database Abstraction ]
tags:
- abstraction
- ado.net
- c#
- linq
- linq to sql
- nhibernate
summary: An open-source project to allow queries to be defined in code and not tied to a specific database implementation
---

When we began developing C# web applications, we found ourselves in the position of determining what the best way of accessing the database is. We evaluated several technologies...

* **NHibernate** - May be very good, but it was overkill for what we were trying to do.
* **LINQ to SQL** - This brings C#'s LINQ (Language-Integrated Query) to SQL databases. You create database-aware classes and use LINQ to select from collections, which LINQ to SQL converts to database access. This is a good abstraction, but it relies on SQL Server; as we typically deploy to PostgreSQL, this didn't work. (We also couldn't get DBLinq, a database-agnostic implementation, to work.)
* **ADO.NET** - This is the tried-and-true database access methodology, released as part of the initial release of the .NET framework. The downside to this is that it encourages SQL in the code at the point of data retrieval; it does not provide a clean separation of data access from data processing.
* **EF Code First** - This didn't exist; it's also very SQL Server-centric. Not faulting Microsoft for that, especially since they release a free version now; but, as we deploy on Linux, until they release a Linux version, SQL Server is not an option.

With our PHP applications, we had written a database service that read queries from XML files. Then, queries were accessed by name, with parameters passed via arrays. The one thing that ADO.NET has that was useful was the fact that it is based on interfaces. This means that if we wrote something that exposed, manipulated, and depended on `IDataConnection` (instead of `SqlConnection`, the SQL Server implementation of that interface), we could support any implementation of database. The `SqlDataReader` implements `IDataReader` as well. Our solution was becoming apparent.

Over time, we developed what is now the [Database Abstraction][proj] project hosted on <del>CodePlex</del> _(UPDATE: migrated project to [GitHub][])_. On Thursday, we released the first public release (although the DLLs are in the repository, and are usually current at every commit). If you are looking for a way to separate your data access from the rest of your code, or want a solution that's database-agnostic, check it out. It supports SQL Server, MySQL, PostgreSQL, SQLite, and ODBC connections *, using the data provider name to derive the proper connection to implement. There is also a Mock implementation to support unit tests; this mock can provide data, providing a useful way to test methods. Finally, there is a membership and role provider based on Database Abstraction; simply configure the connection string, create the database tables, and away you go! **

A pre-released version is already in production use in our [PrayerTracker][] application, and others are being built around it. If this sounds like something that could help your project, certainly feel free to [check it out][rel]!

<small>_\* Oracle is omitted from this list, as their DLL had redistribution restrictions; this meant that the source code repository, upon check-out, would have build errors. There may be an Oracle implementation in the future (it would be trivial), but there is not one now._

<small>_\** The membership and role providers are untested; they will be tested and tweaked by version 0.9._</small>


[proj]:          //dbabstraction.codeplex.com "Database Abstraction &bull; CodePlex"
[PrayerTracker]: //prayer.djs-consulting.com "PrayerTracker"
[rel]:           //dbabstraction.codeplex.com/releases/view/75241 "Database Abstaction v0.8 &bull; Database Abstraction"
[GitHub]:        //github.com/danieljsummers/DatabaseAbstraction
