---
layout: post
title: "A Tour of myPrayerJournal: Conclusion"
date: 2018-09-02 07:10:00
author: Daniel
categories:
- [ Programming, Go ]
- [ Programming, JavaScript ]
- [ Projects, myPrayerJournal ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- bootstrap
- bundle
- community
- css
- element ui
- flexbox
- go
- golang
- grid
- html
- jquery
- lodash
- markdown
- moment
- tree-shaking
- vue
- webpack
- writing
---
_NOTE: This is the final post of an 8-post series; see [the introduction][intro] for all of them, and the requirements for which this software was built._

Over the course of this tour, we've meandered through client side code, server side code, a database, and documentation. However, the experience of developing an application is more than just the sum of the assembled technologies and techniques. Let's take a look at some of these "lessons learned" and opinions formed through this process. (This post will use the first-person singular pronouns "I" / "me" / "my" a lot more than the previous ones.)

## Vue Is Awesome -- But...

As I tried different <abbr title="Single Page Application">SPA</abbr> frameworks, they were interesting and fun, but a lot more work than I expected. Then, I came across [Vue][], and its paradigm and flow just clicked. Single file components are great; it was so nice to not have to go digging through a site-wide CSS file looking for styles that affected the elements in the component. I just had to scroll down! While I did put the common CSS in `App.vue`, the application's top component, anything unique that component did was right there. There are also all kinds of Vue-aware packages that you can add and use, that add their own elements/components to the process; [Element UI][], [Bootstrap Vue][], and [Vue-Awesome][] were three of the ones I used at different points in development. Since it's a JavaScript framework, you can use vanilla JS packages as well; myPrayerJournal uses [moment.js][] to display relative dates ("last activity 8 minutes ago") and format dates for display.

Then... I ran the build process, and the bundles were huge -- as in, multiple megabytes huge! We changed our implementation of Vue-Awesome to only import the icons we used in the application (to be fair to them, that is the project's recommendation). Element also seemed to be rather heavy. One of the last issues I worked before final release was removing Bootstrap Vue and just using straight HTML/CSS for layout and flow (which is another lesson we'll explore below). There are guides on configuring [Webpack][] to help make moment's bundle smaller (and that project has an open issue to refactor so that it's more friendly to a "just import the part you need" paradigm).

None of this is meant as a knock of any of the fine projects I've named up to this point. It was also near the end of the project when I converted to the Vue CLI v3 template, which uses a version of Webpack that has a much better "tree-shaking" algorithm. (This means that, if it can establish that code is never executed, it excludes it from the bundle.) myPrayerJournal v1.0's modern "vendor" bundle (the one with the libraries) is 283K, while the legacy bundle is 307K; while that's not lightning fast on mobile, it's also comparable to a lot of other sites, and since page updates happen through the API, it is fast once it's loaded.<a href="#note-1"><sup>1</sup></a>

**Lesson**: Be smart about what you import.

**Lesson**: The JavaScript ecosystem evolves quickly. This was published September 2nd, 2018, describing development that occurred September 2016 through August 2018; a good bit of this is likely already obsolete. :)

## You Might Not Need...

We mentioned above that the site eventually was written with simple HTML and CSS. Many of the more popular pacakges and utilities were created to make up for deficiencies, either in the browser ecosystem or among the differing browser vendors. With the recent efforts by browser vendors to support published standards, though, many of these packages are used for reasons that distill to comfort and inertia. As before, this is not a knock on these projects; they filled a definite need, and continue to work as the basis for a lot of deployed, executing code.

For new development, though, existing standards -- and their support -- may be sufficient. There are some great sites that detail how certain things can be done using plain JavaScript or CSS.
- [You Might Not Need jQuery][jq]
- [You Might Not Need Bootstrap][boot] _(for this one, you have to read the HTML yourself; looks like it's not hosted at the given URL anymore)_
- [You Might Not Need Lodash][lodash]
- [Can I Use ___?][ciu]

I used the last one quite a bit. I also extensively referred to [CSS Tricks' "A Complete Guide to Flexbox"][flexbox] post. When I decided to rework the layout without Bootstrap, I thought the replacement would be CSS Grid; however, Flexbox was more than enough.

**Lesson**: Use a framework if you want, but don't assume it's the only way to do things.

**Lesson**: If you want to shrink your bundle size, 20-30 lines of your own code can sometimes save you 20-30K (or more).

## Learn Go

> Ladies and gentlemen of the Internet class of 2018 -
> &nbsp; Learn Go.
> If I could offer you only one tip for the future,
> &nbsp; Go would be it.
> -- [with apologies to Baz Luhrmann][ws]

[Go][] is a systems programming language. It was developed at Google, to help them better utilize their hardware. It natively supports concurrent processing (which can be done in parallel, but is distinct from "parallel programming"); has an opinionated code formatter; forces you to address calls that may error; and is terribly efficient. When myPrayerJournal was running with the Go backend, the working size in RAM was around 10MB. Let me say that again, this time with feeling - **the working size for a database-accessing, HTTP-listening, dynamic web service was 10MB of RAM!** If you have ever profiled a web server process, you know that it's nearly ludicrous how small this is. For comparison, the process working set for the F#/Giraffe/EF Core version of the backend runs between 60-80MB, and includes another ~256MB of shared working set memory.<a href="note-2"><sup>2</sup></a> (An Apache2 process running PHP can run in the 256MB range as well.)

Why am I recommending a technology that I ultimately moved away from before the v1.0 release? Well, other than "did you read the last paragraph?!?!", the short answer is "it's the future, and will change how you code in every other language." The fact that it forces you to deal with every single thing that may error makes it robust; but, if you learn to develop with it, you will find yourself thinking about error handling more fully than you did before -- and I say this as a person who already coded error handlers as I coded the happy path.

Why did I move away from a technology on which I'm so bullish? Well, for starters, F# is the language that "clicks" for me in the same way that Vue did as a client-side framework; its development paradigm just makes sense with the way I think about structuring code. I completed a project that used F# and Giraffe (which I hope to take open-source soon), and that gave me the confidence to move forward with a third attempt at an F# API. _(Third time's the charm, right?)_ Also, while Entity Framework generated some pretty verbose SQL statements, EF Core more or less generates what I would have written anyway, **plus** it takes care of populating the objects it returns from the database.

I also found the development process awkward, though not unwieldy. _(They're probably not going to adopt that as their slogan...)_ Much has been written about the `GOPATH` environment variable, but once you get into it, it starts to make sense. `go get` is great at pulling down your dependencies, and the way it does it, you can peruse the source code to see exactly what they are doing. Also, it was not too difficult to develop on Windows, but build executables for Linux -- which, in the "systems" programming work, is a really cool feature.

**Lesson**: Learn Go.

## Write about Your Code

This wasn't a lesson I learned on this project; this was one I'd known for a while. There are _(at least)_ two distinct benefits to writing about your code:
- It can help you learn even more than you may have learned when you were writing the code itself. As developers, we sometimes forget to step back and look at the body of work we've created. If you write about it, you have to form a coherent view about it so you can explain it to others; this helps you long-term. Also, people who know more about the environment can chime in on what you've written, which also not only helps you learn, ...
- It helps build community. If you hit a snag, and someone in the community around that technology helps you get past it, writing about your experience means that the next person to encounter that issue may not have to ask; if their searching leads them to your post, they can fix it and move on. This applies doubly if you could **not** get help from the community; you might be the one who surfaces this issue/technique, and moves the entire community forward.

**Lesson**: Write about your code; participate in the community around your technology to whatever extent you are able.

<p>&nbsp;</p>

If you've been with us for this entire tour -- thank you. I hope you've learned something; I know I have, not just through the development of [myPrayerJournal][], but through the course of writing about it. And, certainly, if you feel that this application could help you in any way, help yourself. It is and will always be free, and [Bit Badger Solutions][bbs] (and DJS Consulting before it) has, as of this writing, a 14-year streak of no known data breaches; your prayer requests are safe with us.

---

<a name="note-1"><sup>1</sup></a> _There are chunk-splitting techniques that can be used to make the initial download smaller, and load other portions on-demand. Moment.js, for example, isn't needed for the default "Welcome to myPrayerJournal" page. We could defer loading that until the user has logged in, as the journal page definitely needs it; we'd still have to download the same amount, but it would be spread out across 2 requests. Opportunities to save some size in the initial download are still out there, but 283K is just above the 244K suggested bundle size, so we went forward with it._

<a name="note-2"><sup>2</sup></a> _The server on which I host myPrayerJournal already has other .NET Core processes running on it, so the shared memory size has already been allocated._


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[Vue]: https://vuejs.org "Vue"
[Element UI]: https://github.com/ElemeFE/element "Element UI | GitHub"
[Bootstrap Vue]: https://bootstrap-vue.js.org "Bootstrap Vue"
[Vue-Awesome]: https://github.com/Justineo/vue-awesome "Vue-Awesome | GitHub"
[moment.js]: https://momentjs.com "Moment.js"
[Webpack]: https://webpack.js.org "Webpack"
[jq]: http://youmightnotneedjquery.com "You Might Not Need jQuery"
[boot]: https://github.com/stuyam/YouMightNotNeedBootstrap/tree/gh-pages "You Might Not Need Bootstrap | GitHub"
[lodash]: https://youmightnotneed.com/lodash "You Might Not Need Lodash"
[ciu]: https://caniuse.com "Can I Use ___?"
[flexbox]: https://css-tricks.com/snippets/css/a-guide-to-flexbox/ "A Complete Guide to Flexbox | CSS Tricks"
[ws]: http://www.metrolyrics.com/everybodys-free-to-wear-sunscreen-lyrics-baz-luhrmann.html "Everybody's Free (to Wear Sunscreen) - Baz Luhrmann | Metro Lyrics"
[Go]: https://golang.org "The Go Programming Language"
[myPrayerJournal]: https://prayerjournal.me "myPrayerJournal"
[bbs]: https://bitbadger.solutions "Bit Badger Solutions"
