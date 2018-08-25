---
layout: post
title: "A Tour of myPrayerJournal: The Front End"
date: 2018-08-25 12:15:00
author: Daniel
categories:
- [ Programming, JavaScript, Vue ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- components
- javascript
- layout
- pug
- router
- routing
- script
- style
- template
- toast
- vue
---
_NOTES:_
- _This is post 2 in a series; see [the introduction][intro] for all of them, and the requirements for which this software was built._
- _Links that start with the text "mpj:" are links to the 1.0.0 tag (1.0 release) of myPrayerJournal, unless otherwise noted._

[Vue][] is a front-end JavaScript framework that aims to have very little boilerplate and ceremony, while still presenting a componentized abstraction that can scale to enterprise-level if required<a href="#note-1"><sup>1</sup></a>. Vue components can be coded using inline templates or multiple files (splitting code and template). Vue also provides Single File Components (SFCs, using the `.vue` extension), which allow you to put template, code, and style all in the same spot; these encapsulate the component, but allow all three parts to be expressed as if they were in separate files (rather than, for example, having an HTML snippet as a string in a JavaScript file). The Vetur plugin for [Visual Studio Code][vscode] provides syntax coloring support for each of the three sections of the file.

## Layout

Using the default template, `main.js` is the entry point; it creates a Vue instance and attaches it to an element named `app`. This file also supports registering common components, so they do not have to be specifically imported and referenced in components that wish to use them. For myPrayerJournal, we registered our common components there ([mpj:main.js][main.js]). We also registered a few third-party Vue components to support a progress bar (activated during API activity) and toasts (pop-up notifications).

`App.vue` is also part of the default template, and is the component that `main.js` attaches to the `app` elements ([mpj:App.vue][App.vue]). It serves as the main template for our application; if you have done much template work, you'll likely recognize the familiar pattern of header/content/footer.

This is also our first look at an SFC, so let's dig in there. The top part is the template; we used [Pug (formerly Jade)][pug] for our templates. The next part is enclosed in `script` tags, and is the script for the page. For this component, we import one additional component (`Navigation.vue`) and the version from `package.json`, then export an object that conforms to Vue's expected component structure. Finally, styles for the component are enclosed in `style` tags. If the `scoped` attribute is present on the style tag, Vue will generate data attributes for each element, and render the declared styles as only affecting elements with that attribute. myPrayerJournal doesn't use scoped styles that much; Vue recommends classes instead, if practical, to reduce complexity in the compiled app.

Also of note in `App.js` is the code surrounding the use of the `toast` component. In the template, it's declared as `toast(ref='toast')`. Although we registered it in `main.js` and can use it anywhere, if we put it in other components, they create their own instance of it. The `ref` attribute causes Vue to generate a reference to that element in the component's `$refs` collection. This enables us to, from any component loaded by the router (which we'll discuss a bit later), access the toast instance by using `this.$parent.$refs.toast`, which allows us to send toasts whenever we want, and have the one instance handle showing them and fading them out. (Without this, toasts would appear on top of each other, because the independent instances have no idea what the others are currently showing.)

## Routing

Just as URLs are important in a regular application, they are important in a Vue app. The Vue router is a separate component, but can be included in the new project template via the Vue CLI. In `App.vue`, the `router-view` item renders the output from the router; we wire in the router in `main.js`. Configuring the router ([mpj:router.js][router.js]) is rather straightforward:
- Import all of the components that should appear to be a page (i.e., not modals or common components)
- Assign each route a path and name, and specify the component
- For URLs that contain data (a segment starting with `:`), ensure `props: true` is part of the route configuration

The `scrollBehavior` function, as it appears in the source, makes the Vue app mimic how a traditional web application would handle scrolling. If the user presses the back button, or you programmatically go back 1 page in history, the page will return to the point where it was previously, not the top of the page.

To specify a link to a route, we use the `router-link` tag rather than a plain `a` tag. This tag takes a `:to` parameter, which is an object with a `name` property; if it requires parameters/properties, a `params` property is included. [mpj:Navigation.vue][Navigation.vue] is littered with the former; see the `showEdit` method in [mpj:RequestCard.vue][RequestCard.vue] for the structure on the latter (and also an example of programmatic navigation vs. `router-link`).

## Components

When software developers hear "components," they generally think of reusable pieces of software that can be pulled together to make a system. While that isn't wrong, it's important to understand that "reusable" does not necessarily mean "reused." For example, the privacy policy ([mpj:PrivacyPolicy.vue][PrivacyPolicy.vue]) is a component, but reusing it throughout the application would be... well, let's just say a "sub-optimal" user experience.

However, that does not mean that none of our components will be reused. `RequestCard`, which we referenced above, is used in a loop in the `Journal` component ([mpj:Journal.vue][Journal.vue]); it is reused for every request in the journal. In fact, it is reused even for requests that should not be shown; behavior associated with the `shouldDisplay` property makes the component display nothing if a request is snoozed or is in a recurrence period. Instead of the journal being responsible for answering the question "Should I display this request?", the request display answers the question "Should I render anything?". This may seem different from typical server-side page generation logic, but it will make more sense once we discuss state management (next post).

This is also a good time to mention component hierarchies; in the current structure, `RequestCard` will always have `Journal` as a parent, and `Journal` will always have `App` as its parent. This means that `RequestCard` could, technically, get its toast implementation via `this.$parent.$parent.toast`; however, this type of coupling is very fragile<a href="#note-2"><sup>2</sup></a>. Requiring `toast` as a parameter to `RequestCard` means that, wherever `RequestCard` is implemented, if it's given a `toast` parameter, it can display toasts for the actions that would occur on that request. `Journal`, as a direct descendant from `App`, can get its reference to the toast instance from its parent, then pass it along to child components; this only gives us one layer of dependency.

Looking at some other reusable (and reused) components, the page title component ([mpj:PageTitle.vue][PageTitle.vue]) changes the title on the HTML document, and optionally also displays a title at the top of the page. The "date from now" component ([mpj:DateFromNow.vue][DateFromNow.vue]) is the most frequently reused component. Every time it is called, it generates a relative date, with the actual date/time as a tool tip; it also sets a timeout to update this every 10 seconds. This keeps the relative time in sync, even if the router destination stays active for a long time.

Finally, it's also worth mentioning that SFCs do not have to have all three sections defined. Thanks to conventions, and depending on your intended use, none of the sections are required. The "date from now" component only has a `script` section, while the privacy policy component only has a `template` section.

<p>&nbsp;</p>

That wraps up our tour of Vue routes and components; next time, we'll take a look at Vuex, and how it helps us maintain state in the browser.

---
<a name="note-1"><sup>1</sup></a> _(That's my summary; I'm sure they've got much more eloquent ways to describe it.)_

<a name="note-2"><sup>2</sup></a> _...and kinda ugly, but maybe that's just me._


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[Vue]: https://vuejs.org
[main.js]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/main.js "app/src/main.js | myPrayerJournal | GitHub"
[App.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/App.vue "app/src/App.vue | myPrayerJournal | GitHub"
[pug]: https://pugjs.org
[vscode]: https://code.visualstudio.com
[router.js]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/router.js "app/src/router.js | myPrayerJournal | GitHub"
[Navigation.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/common/Navigation.vue "app/src/components/common/Navigation.vue | myPrayerJournal | GitHub"
[RequestCard.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/request/RequestCard.vue#L45 "app/src/components/request/RequestCard.vue | myPrayerJournal | GitHub"
[PrivacyPolicy.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/legal/PrivacyPolicy.vue "app/src/components/legal/PrivacyPolicy.vue | myPrayerJournal | GitHub"
[Journal.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/Journal.vue "app/src/components/Journal.vue | myPrayerJournal | GitHub"
[PageTitle.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/common/PageTitle.vue "app/src/components/common/PageTitle.vue | myPrayerJournal | GitHub"
[DateFromNow.vue]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/app/src/components/common/DateFromNow.vue "app/src/components/common/DateFromNow.vue | myPrayerJournal | GitHub"
