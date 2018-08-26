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

[Auth0][] provides authentication services; they focus on one thing, and getting that one thing right. They support simple username/password authentication, as well as integrations with many other providers. As "minimalist" was one of our goals, not having to build yet another user system was appealing. As an open source project, Auth0 provides these services at no cost. They're the organization behind the [JSON Web Token (JWT)][JWT] standard, which allows base-64-encoded, encrypted JSON to be passed around as proof of identity.

This decision has proved to be a good one. In the introduction, we mentioned all of the different frameworks and server technologies we had used before settling on the one we did. In all but one of these "roads not further traveled"<a href="#note-1"><sup>1</sup></a>, authentication worked. They have several options for how to use their service; you can bring in their library and host it yourself, you can write your own and make your own calls to their endpoints, or you can use their hosted version. We opted for the latter.

## Integrating Auth0 in the App

JavaScript seems to be Auth0's primary language. They provide an [npm package][npm] to support using the responses that will be returned from their hosted login page. The basic flow is:
- The user clicks a link that executes their `authorize()` function
- The user completes authorization through Auth0
- Auth0 returns the result and JWT to a predefined endpoint in the app
- The app uses their `parseHash()` function to extract the JWT from the URL (a `GET` request)
- If everything is good, establish the user's session and proceed

myPrayerJournal's implementation is contained in `AuthService.js` ([mpj:AuthService.js][AuthService.js]). There is a file that is not part of the source code repository; this is the file that contains the configuration variables for the Auth0 instance. Using these variables, we configure the `WebAuth` instance from the Auth0 package; this instance becomes the execution point for our other authentication calls.

## Using JWTs in the App

We'll start easy. The `login()` function simply exposes Auth0's `authorize()` function, which directs the user to the hosted log on page.

The next in logical sequence, `handleAuthentication()`, is called by `LogOn.vue` ([mpj:LogOn.vue][LogOn.vue]) on line 16, passing in our store and the router. (In our last post, we discussed how server requests to a URL handled by the app should simply return the app, so that in can process the request; this is one of those cases.) `handleAuthentication()` does several things:
- It calls `parseHash()` to extract the JWT from the request's query string.
- If we got both an access token and an ID token:
    - It calls `setSession()`, which saves these to local storage, and schedules renewal (which we'll discuss more in a bit).
    - It then calls Auth0's `userInfo()` function to retrieve the user profile for the token we just received.
    - When that comes back, it calls the store's ([mpj:store/index.js][store]) `USER_LOGGED_ON` mutation, passing the user profile; the mutation saves the profile to the store, local storage, and sets the `Bearer` token on the API service (more on that below as well).
    - Finally, it replaces the current location (`/user/log-on?[lots-of-base64-stuff]`) with the URL `/journal`; this navigates the user to their journal.
- If something didn't go right, we log to the console and pop up an alert. There may be a more elegant way to handle this, but in testing, the only way to reliably make this pop up was to mess with things. If you're messing with things, you don't get nice error messages.

Let's dive into the store's `USER_LOGGED_ON` mutation a bit more; it starts on line 68. The local storage item and the state mutations are pretty straightforward, but what about that `api.setBearer()` call? The API service ([mpj:api/index.js][api]) handles all the API calls through the [Axios][] library. Axios supports defining default headers that should be sent with every request, and we'll use the HTTP `Authorization: Bearer [base64-jwt]` header to tell the API what user is logged in. Line 18 sets the default `authorization` header to use for all future requests. (Back in the store, note that the `USER_LOGGED_OFF` mutation (just above this) does the opposite; it clears the `authorization` header. The `logout()` function in `AuthService.js` clears the local storage.)

At this point, once the user is logged in, the `Bearer` token is sent with every API call. Each component, nor the store or its actions, need to do anything differently; it just works.

## Maintaining Authentication

JWTs have short expirations, usually expressed in hours. Have a user's authentication go stale is not good! The `scheduleRenewal()` function in `AuthService.js` schedules a behind-the-scenes renewal of the JWT. When the time for renewal arrives, `renewToken()` is called, and if the renewal is successful, it runs the result through `setSession()`, just as we did above, which schedules the next renewal as its last step.

For this to work, we had to add `/static/silent.html` as an authorized callback for Auth0. This is an HTML page that sits outside of the Vue app; however, the `usePostMessage: true` parameter tells the renewal call that it will receive its result from a `postMessage` call. `silent.html` uses the Auth0 library to parse the hash and post the result to the parent window.<a href="#note-2"><sup>2</sup></a>

## Using JWTs in the API

Now that we're sending a `Bearer` token to the API, the API can tell if a user is logged in. We looked at some of the handlers that help us do that when we looked at the API in depth. Let's return to those and see how that is.

Before we look at the handlers, though, we need to look at the configuration, contained in `Program.fs` ([mpj:Program.fs][Program.fs]). You may remember that Giraffe sits atop ASP.NET Core; we can utilize it's `JwtBearer` methods to set everything up. Lines 38-48 are the interesting ones for us; we use the `UseAuthentication` extension method to set up JWT handling, then use the `AddJwtBearer` extension method to configure our specific JWT values. (As with the app, these are part of a file that is not in the repository.) The end result of this configuration is that, if there is a `Bearer` token that is a valid JWT, the `User` property of the `HttpContext` has an instance of the `ClaimsPrincipal` type, and the various properties from the JWT's payload are registered as `Claims` on that user.

Now we can turn our attention to the handlers ([mpj:Handlers.fs][Handlers.fs]). `authorize`, on line 72, calls `user ctx`, which is defined on lines 50-51. All this does is look for a claim of the type `ClaimTypes.NameIdentifier`. This can be non-intuitive, as the source for this is the `sub` property from the JWT<a href="#note-3"><sup>3</sup></a>. A valid JWT with a `sub` claim is how we tell we have a logged on user.

You may have noticed that, when we were describing the entities for the API, we did not mention a `User` type. The reason for that is simple; the only user information it stores is the `sub`. `Request`s are assigned by user ID, and the user ID is included with every attempt to make any change to a request. This eliminates URL hacking or rogue API posting being able to get anything meaningful from the API.

The `userId` function, just below the `user` function, extracts this claim and returns its value, and it's used throughout the remainder of `Handlers.fs`. `add` (line 160) uses it to set the user ID for a new request. `addHistory` (line 192) and `addNote` (line 218) both use the user ID, as well as the passed request ID, to try to retrieve the request before adding history or notes to it. `journal` (line 137) uses it to retrieve the journal by user ID.

<p>&nbsp;</p>

We now have a complete application, with the same user session providing access to the Vue app and tying all API calls to that user. We also use it to maintain data security among users, while truly outsourcing all user data to Microsoft or Google (the two external providers currently registered). We do still have a few more stops on our tour, though; the next is the back end data store.

---

<a name="note-1"><sup>1</sup></a> _Sorry, Elm; it's not you, it's me..._

<a name="note-2"><sup>2</sup></a> _This does work, but not indefinitely; if I leave the same browser window open from the previous day, I still have to sign in again. I very well could be "doing it wrong;" this is an area where I probably experienced the most learning through creating this project._

<a name="note-3"><sup>3</sup></a> _I won't share how long it took me to figure out that `sub` mapped to that; let's just categorize it as "too long." With a Microsoft login, it's the only one that doesn't come across by its JWT name._


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[Auth0]: https://auth0.com
[JWT]: https://jwt.io
[npm]: https://www.npmjs.com/package/auth0-js
[AuthService.js]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/auth/AuthService.js "app/src/auth/AuthService.js | myPrayerJournal | GitHub"
[LogOn.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/user/LogOn.vue "app/src/components/user/LogOn.vue | myPrayerJournal | GitHub"
[store]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/store/index.js "app/src/store/index.js | myPrayerJournal | GitHub"
[api]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/api/index.js "app/src/api/index.js | myPrayerJournal | GitHub"
[Axios]: https://www.npmjs.com/package/axios
[Program.fs]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Program.fs "api/Program.fs | myPrayerJournal | GitHub"
[Handlers.fs]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Handlers.fs "api/Handlers.fs | myPrayerJournal | GitHub"
