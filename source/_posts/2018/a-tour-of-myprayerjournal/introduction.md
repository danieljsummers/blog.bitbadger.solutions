---
layout: post
title: "A Tour of myPrayerJournal: Introduction"
date: 2018-08-24 12:15:00
author: Daniel
categories:
- [ Databases, PostgreSQL ]
- [ Programming, .NET, F# ]
- [ Programming, JavaScript, Vue ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- angular
- aurelia
- elm
- f#
- giraffe
- go
- golang
- introduction
- javascript
- journaling
- koa
- node
- prayer
- requirements
- suave
- vue
---
Recently, we released version 1.0 of [myPrayerJournal][], a minimalistic prayer journaling application. This series aims to provide a tour of the code, with several stops along the way:

- **Part 0: Introduction** _(this post)_
- **[Part 1: The Front End][part1]** - Vue components and routing
- **[Part 2: State in the Browser][part2]** - Vuex and getting information from an API
- **[Part 3: The API][part3]** - Giraffe and JSON web endpoints
- **Part 4: Authentication** - Auth0, using information in both app and API
- **Part 5: The Data Store** - EF Core backed by PostgreSQL, with the `DbContext` defined in F#
- **Part 6: Documentation** - GitHub Pages generated on each commit

_(these will be linked once each post has been published)_

From a technical perspective, this application was going to be a learning experience. We knew we wanted to use a Single Page Application (SPA) framework with an API; we'd built APIs before, but had yet to build a SPA. For front-end frameworks, we started with [Angular][], went through [Aurelia][] and [Elm][], then decided on [Vue][]. For the back-end API, we started with [Suave][], then went live on [Node.js][] with [Koa][]; later, we moved it to [Go][], and after .NET Core 2.1 was released, landed on [Giraffe][]. The "learning experience" part was a success; through all these attempts, we utilized 5 different languages and 3 different database access techniques.

To understand the requirements, a short explanation of the process will help. "Prayer journaling" is a discipline where a person will write down the things for which they are praying; this provides a defined list to help guide their prayer, and helps them not forget things. Then, as the situation changes, they can record updates, through to the resolution of the situation (also called the request being "answered"). This discipline not only helps to focus efforts, it also provides a record of requests and answers. Although people have successfully used a notebook, or something similar, for a long time, that approach does have some downsides:
- For long term requests, you can run out of room for updates.
- A physical journal can only be in one place at one time.
- Answered requests coexist with unanswered requests, so you have to flip pages past them.
- Books can end up under stacks of other things, falling victim to "out of sight, out of mind."

Looking to address some of those, the initial requirements started as the first three bullets below. The remaining requirements emerged through using the application as it was being developed.
- List unanswered requests, in a way that they can be marked as prayed or answered, and be updated
- List answered requests, and allow full requests (and their history) to be viewed
- Do the above in a way that will not be distracting
- Allow notes to be recorded for a request; not every update on a situation requires a change in the verbiage of the request
- Allow requests to be "snoozed" (removed from the journal, with a specified date when they will reappear), and list snoozed requests so that the snooze can be expired (returning the request to the journal immediately)
- Allow requests to be prioritized (this became the request recurrence feature)

Armed with these requirements, we will pick up next time with a look at the Vue front end.


[myPrayerJournal]: https://github.com/bit-badger/myPrayerJournal/tree/1.0.0
[part1]: /2018/a-tour-of-myprayerjournal/the-front-end.html "A Tour of myPrayerJournal: The Front End | The Bit Badger Blog"
[part2]: /2018/a-tour-of-myprayerjournal/state-in-the-browser.html "A Tour of myPrayerJournal: State in the Browser | The Bit Badger Blog"
[part3]: /2018/a-tour-of-myprayerjournal/the-api.html "A Tour of myPrayerJournal: The API | The Bit Badger Blog"
[Angular]: https://angular.io
[Aurelia]: https://aurelia.io
[Elm]: http://elm-lang.org
[Vue]: https://vuejs.org
[Suave]: https://suave.io
[Node.js]: https://nodejs.org
[Koa]: https://koajs.com
[Go]: https://golang.org
[Giraffe]: https://github.com/giraffe-fsharp/Giraffe
