---
layout: post
title: "A Tour of myPrayerJournal: State in the Browser"
date: 2018-08-26 12:15:00
author: Daniel
categories:
- [ Programming, JavaScript, Vue ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- action
- components
- facebook
- flux
- javascript
- mutation
- react
- redux
- script
- state
- vue
- vuex
---
_NOTES:_
- _This is post 3 in a series; see [the introduction][intro] for all of them, and the requirements for which this software was built._
- _Links that start with the text "mpj:" are links to the 1.0.0 tag (1.0 release) of myPrayerJournal, unless otherwise noted._

Flux (a pattern that originated at Facebook) defines state, as well as actions that can mutate that state. Redux is the most popular implementation of that pattern, and naturally works very well with React. However, other JavaScript framewoks use this pattern, as it ensures that state is managed sanely. (Well, the state is sane, but so is the developer!)

As part of Vue, the [Vuex][] component is a flux implementation for Vue that provides a standard way of managing state. They explain it in much more detail, so if the concept is a new one, you may want to read their "What is Vuex?" page before you continue. Once you are ready, let's continue and take a look at how it's used in myPrayerJournal.

## Defining the State

The store ([mpj:store/index.js][store]) exports a single new `Vuex.Store` instance, with its `state` property defining the items it will track, along with initial values for those items. This represents the initial state of the app, and is run whenever the browser is refreshed.

In looking at our store, there are 4 items that are tracked; two items are related to authentication, and two are related to the journal. As part of authentication (which will get a further exploration in its own post), we store the user's profile and identity token in local storage; the initial values for those items attempt to access those values. The two journal related items are simply initialized to an empty state.

## Mutating the State

There are a few guiding principles for mutations in Vuex. First, they must be defined as part of the `mutations` property in the store; outside code cannot simply change one state value to another without going through a mutation. Second, they must be synchronous; mutations must be a fast operation, and must be accomplished in sequence, to prevent race conditions and other inconsistencies. Third, mutations cannot be called directly; mutations are "committed" against the current store. Mutations receive the current state as their first parameter, and can receive as many other parameters as necessary.

_(Side note: although these functions are called "mutations," Vuex is actually replacing the state on every call. This enables some really cool time-traveling debugging, as tools can replay states and their transformations.)_

So, what do you do when you need to run an asynchronous process - like, say, calling an API to get the requests for the journal? These processes are called actions, and are defined on the `actions` property of the store. Actions receive an object that has the state, but it also has a `commit` property that can be used to commit mutations.

If you look at [line 87 of store/index.js][line87], you'll see the above concepts put into action<a href="#note-1"><sup>1</sup></a> as a user's journal is loaded. This one action can commit up to 4 mutations of state. The first clears out whatever was in the journal before, committing the `LOADED_JOURNAL` mutation with an empty object. The second sets the `isLoadingJournal` property to `true` via the `LOADING_JOURNAL` mutation. The third, called if the API call resolves successfully, commits the `LOADED_JOURNAL` mutation with the results. The fourth, called whether it works or not, commits `LOADING_JOURNAL` again, this time with `false` as the parameter.

A note about the names of our mutations and actions - the Vuex team recommends defining constants for mutations and actions, to ensure that they are defined the same way in both the store, and in the code that's calling it. This code follows their recommendations, and those are defined in `action-types.js` and `mutation-types.js` in the `store` directory.

## Using the Store

So, we have this nice data store with a finite number of ways it can be mutated, but we have yet to use it. Since we looked at loading the journal, let's use it as our example ([mpj:Journal.vue][Journal.vue]). On line 56, we wrap up the computed properties with `...mapState`, which exposes data items from the store as properties on the component. Just below that, the `created` function calls into the store, exposed as `$store` on the component instance, to execute the `LOAD_JOURNAL` action.

The template uses the mapped state properties to control the display. On lines 4 and 5, we display one thing if the `isLoadingJournal` property is true, and another (which is really the rest of the template) if it is not. Line 12 uses the `journal` property to display a `RequestCard` ([mpj:RequestCard.vue][RequestCard.vue]) for each request in the journal.

I mentioned developer sanity above; and in the [last post][part1], I said that the logic that has `RequestCard` making the decision on whether it should show, instead of `Journal` deciding which ones it should show, would make sense. This is where we put those pieces together. The Vuex store is reactive; when data from it is rendered into the app, Vue will update the rendering if the store changes. So, `Journal` simply displays a "hang on" note when the journal is loading, and "all the requests" once it's loaded. `RequestCard` only displays if the request should be displayed. And, the entire "brains" behind this is the thing that starts the entire process, the call to the `LOAD_JOURNAL` action. We aren't moving things around, we're simply displaying the state of things as they are!

`Navigation` ([mpj:Navigation.vue][Navigation.vue]) is another component that bases its display off state, and takes advantage of the state's reactivity. By mapping `isAuthenticated`, many of the menu items can be shown or hidden based on whether a user is signed in or not. Through mapping `journal` and the computed property `hasSnoozed`, the "Snoozed" menu link does not show if there are no snoozed requests; however, the first time a request from the journal is snoozed, this one appears _**just because the state changed**_.

This is one of the things that cemented the decision to use Vue for the front end<a href="#note-2"><sup>2</sup></a>, and is one of my favorite features of the entire application. _(You've probably picked up on that already, though.)_

<p>&nbsp;</p>

We've now toured our stateful front end; next time, we'll take a look at the API we use to get data into it.

---
<a name="note-1"><sup>1</sup></a> _Pun not originally intended, but it is now!_

<a name="note-2"><sup>2</sup></a> _The others were the lack of ceremony and the Single File Component structure; both of those seem quite intuitive._


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[Vuex]: https://vuex.vuejs.org
[store]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/store/index.js "app/src/store/index.js | myPrayerJournal | GitHub"
[line87]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/store/index.js#L87 "app/src/store/index.js (line 87) | myPrayerJournal | GitHub"
[Journal.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/Journal.vue "app/src/components/Journal.vue | myPrayerJournal | GitHub"
[RequestCard.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/request/RequestCard.vue "app/src/components/request/RequestCard.vue | myPrayerJournal | GitHub"
[part1]: /2018/a-tour-of-myprayerjournal/the-front-end.html#Components "Components | A Tour of myPrayerJournal: The Front End | The Bit Badger Blog"
[Navigation.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/common/Navigation.vue "app/src/components/common/Navigation.vue | myPrayerJournal | GitHub"
