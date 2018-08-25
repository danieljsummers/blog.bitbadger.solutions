---
layout: post
title: "A Tour of myPrayerJournal: Authentication"
date: 2018-08-28 12:15:00
author: Daniel
categories:
- [ Programming, JavaScript, Vue ]
- [ Programming, .NET, F# ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- api
- auth0
- f#
- giraffe
- handler
- javascript
- json
- npm
- spa
---
_NOTES:_
- _This is post 5 in a series; see [the introduction][intro] for all of them, and the requirements for which this software was built._
- _Links that start with the text "mpj:" are links to the 1.0.0 tag (1.0 release) of myPrayerJournal, unless otherwise noted._

At this point in our tour, we're going to shift to a cross-cutting concern for both app and API - authentication. While authentication and authorization are distinct concerns, the authorization check in myPrayerJournal is simply "Are you authenticated?" So, while we'll touch on authorization, and it will seem like a synonym for authentication, remember that they would not be so in a more complex application.

## Deciding on Auth0

[Auth0][] provides authentication services; they focus on one thing, and getting that one thing right. They support simple username/password authentication, as well as integrations with many other providers. As "minimalist" was one of our goals, not having to build yet another user system was appealing. As an open source project, Auth0 provides these services at no cost.

This decision has proved to be a good one. In the introduction, we mentioned all of the different frameworks and server technologies we had used before settling on the one we did. In all but one of these "roads not further traveled"<a href="#note-1"><sup>1</sup></a>, authentication worked. They have several options for how to use their service; you can bring in their library and host it yourself, you can write your own and make your own calls to their endpoints, or you can use their hosted version. We opted for the latter.

## Integrating Auth0 in the App

JavaScript seems to be Auth0's primary language. They provide an [npm package][npm] to support using the responses that will be returned from their hosted login page.

---

<a name="note-1"><sup>1</sup></a> _Sorry, Elm; it's not you, it's me..._


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[Auth0]: https://auth0.com
[npm]: https://www.npmjs.com/package/auth0-js
