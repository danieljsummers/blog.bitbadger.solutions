---
layout: post
title: "A Tour of myPrayerJournal: The Data Store"
date: 2018-08-30 12:15:00
author: Daniel
categories:
- [ Programming, .NET, F# ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- api
- f#
---
_NOTES:_
- _This is post 6 in a series; see [the introduction][intro] for all of them, and the requirements for which this software was built._
- _Links that start with the text "mpj:" are links to the 1.0.0 tag (1.0 release) of myPrayerJournal, unless otherwise noted._

Up to this point in our tour, we've talked about data a good bit, but it has all been in the context of whatever else we were discussing. Let's dig into the data structure a bit, to see how our information is persisted and retrieved.

## Conceptual Design

The initial thought was to create a document store with one document type, the request. The request would have an ID, the ID of the user who created it, and an array of updates/records. Through the initial phases of development, our preferred document database ([RethinkDB][]) was going through a tough period, with their company shutting down; thankfully, they're now part of the Linux Foundation, so they're still around. RethinkDB supports calculated fields in documents, so the plan was to have a few of those to keep us from having to retrieve or search through the array of updates.

We also considered a similar design using [PostgreSQL][]'s native <abbr title="JavaScript Object Notation">JSON</abbr> support. While it does not natively support calculated fields, a creative set of indexes could also suffice. As we thought it through a little more, though, this seemed to be over-engineering; this isn't unstructured data, and PostgreSQL handles max-length character fields very well. (This is supposed to be a "minimalist" application, right?) A relational structure would fit our needs quite nicely.

The starting design, then, used 2 tables. `request` had an ID and a user ID; `history` had the request ID, an "as of" date, a status (created, updated, etc.), and the optional text associated with that update. Early in development, the `journal` view brought together the request/user IDs along with the latest history entry that affected the text of the request, as well as the last date/time an action had occurred on the request. When the notes capability was added, it got its own `note` table, with a structure similar to the `history` table, but without a status, and the text is not optional. As snoozing and recurrence capabilities were added, those fields were added to the `request` table (and the `journal` view).

The final design uses 3 tables, 2 of which have a one-to-many relationship with the third; and 1 view, which provides the calculated fields we were going to have RethinkDB calculate for us.

## Database Changes (Migrations)

As we ended up using 3 different server environments over the course of this project, we ended up writing a `DbContext` class based on our existing structure. For the Node.js backend, we created a <abbr title="Data Definition Language">DDL</abbr> file ([mpj:ddl.js][ddl.js], v0.8.4+) that checked for the existence of each table and view, and also had the SQL to execute if the check failed. For the Go version ([mpj:data.go][data.go], v0.9.6+), the `EnsureDB` function does a similar thing; looking at line 347, it is checking for a specific column in the `request` table, and running the `ALTER TABLE` statement to add it if it isn't there.

The only change that was required since the F#/Giraffe backend has been in place was the one to support request recurrence. Since we did not end up with a scaffolded EF Core initial migration/model, we simply wrote a SQL script to accomplish these changes ([mpj:sql directory][sql]).<a href="#note-1"><sup>1</sup></a>

## The EF Core Model

EF Core uses the familiar `DbContext` class from prior versions of Entity Framework. myPrayerJournal does take advantage of a feature that just arrived in EF Core 2.1, though - the `DbQuery` type. `DbSet`s are collections of entities that generally map to an underlying database table. They can be mapped to views, but unless it's an updateable view, updating those entities results in a runtime error; plus, since they can't be updated, there's no need for the change tracking mechanism to care about the entities returned. `DbQuery` addresses both these concerns, providing lightweight read-only access to data from views.

The `DbContext` class is defined in Data.fs ([mpj:Data.fs][Data.fs]), starting in line 189. It's relatively straightforward, though if you have only ever seen a C# model, it's a bit different. The combination of `val mutable x : [type]` and the `[<DefaultValue>]` attribute are the F# equivalent of C#'s `[type] x;` declaration, which creates a variable and initializes reference types to `null`. The EF Core runtime provides these instances to their setters (lines 203, 206, 209, and 212), and the application code uses them via the getters (a line earlier, each).

The `OnModelCreating` overridden method (line 214) is called when the runtime first creates its instance of the data model. Within this method, we call the `.configureEF` function of each of our database types. The name of this function isn't prescribed, and we could define the entire model without even referencing the data types of our entities; however, this technique gives us a "configure where it's defined" paradigm with each entity type. While the EF "Code First" model creates tables that don't need a lot of configuring; sine we're writing a `DbContext` to target an existing database, we must provide more information about the layout of the database tables.

Let's start out by taking a look at `History.configureEF` (line 50). Line 53 says that we're going to the table `history`. This seems to be a no-brainer, but EF Core would (by convention) be expecting a `History` table; since PostgreSQL uses a different syntax for case-sensitive names, these queries would look like `SELECT ... FROM "History" ...`, resulting in a nice "relation does not exist" error. Line 54 defines our compound key (`requestId` and `asOf`). Lines 55-57 define certain properties of the entity as required; if we try to store an entity where these fields are not set, the runtime will raise an exception before even trying to take it to the database. _(F#'s non-nullability makes this a non-issue, but it still needs to be defined to match the database.)_ Line 58 may seem to do nothing, but what it does is make the `text` property immediately visible to the model builder; then, we can define an `OptionConverter<string>`<a href="#note-2"><sup>2</sup></a> for it, which will translate between `null` and `string option` (`None` = `null`, `Some [x]` = `[x]`). _(Lines 60-61 are left over from when I was trying to figure out why line 62 was raising an exception, leading to the addition of line 58; they could safely be remove, and will be for a post-1.0 release.)_

`History` is the most complex configuration, but let's take a peek at `Request.configureEF` (line 126) to see one more interesting technique. Lines 107-110 define the `history` and `notes` collections on the `Request` type; lines 138-145 define the one-to-many relationship (without a foreign key entity in the child types). Note the casts to `IEnumerable<x>` (lines 138 and 142) and `obj` (lines 140 and 144); while F# is good about inferring types in a lot of cases, these functions are two places it is not. We can use the `:>` operator for the cast, because these types are part of the inheritance chain. _(The `:?>` operator is used for potentially unsafe cases.)_

## Reading and Writing Data

<p>&nbsp;</p>

wrap

---

<a name="note-1"><sup>1</sup></a> _Writing this post has shown me that I need to either create a SQL creation script for the repo, or create an EF Core initial migration/model, so the database ever has to be recreated from scratch. It's good to write about things after you do them!_

<a name="note-2"><sup>2</sup></a> _This is also a package I wrote; it's [available on NuGet][oc-pkg], and I also wrote [a post about what it does][oc-post]._


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[RethinkDB]: https://rethinkdb.com
[PostgreSQL]: https://www.postgresql.org
[ddl]: https://github.com/bit-badger/myPrayerJournal/blob/3c3f0a7981fa8f82d3cc904630960ca43c910cd2/src/api/src/db/ddl.js "api/db/ddl.js | myPrayerJournal | GitHub"
[data.go]: https://github.com/bit-badger/myPrayerJournal/blob/d0ea7cf3c631512ea6b3afba61a25c83aaded6c8/src/api/data/data.go#L307 "api/data/data.go (Line 307) | myPrayerJournal | GitHub"
[sql]: https://github.com/bit-badger/myPrayerJournal/tree/1.0.0/src/sql "sql | myPrayerJournal | GitHub"
[Data.fs]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Data.fs "api/Data.fs | myPrayerJournal | GitHub"

[oc-pkg]: https://www.nuget.org/packages/FSharp.EFCore.OptionConverter/ "FSharp.OptionConverter | NuGet"
[oc-post]: https://blog.bitbadger.solutions/2018/f-sharp-options-with-ef-core.html "F# Options with EF Core | The Bit Badger Blog"