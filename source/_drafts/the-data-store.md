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


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[RethinkDB]: https://rethinkdb.com
[PostgreSQL]: https://www.postgresql.org
[ddl]: https://github.com/bit-badger/myPrayerJournal/blob/3c3f0a7981fa8f82d3cc904630960ca43c910cd2/src/api/src/db/ddl.js "api/db/ddl.js | myPrayerJournal | GitHub"
[data.go]: https://github.com/bit-badger/myPrayerJournal/blob/d0ea7cf3c631512ea6b3afba61a25c83aaded6c8/src/api/data/data.go#L307 "api/data/data.go (Line 307) | myPrayerJournal | GitHub"
