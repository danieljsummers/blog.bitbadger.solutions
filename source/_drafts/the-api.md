---
layout: post
title: "A Tour of myPrayerJournal: The API"
date: 2018-08-27 12:15:00
author: Daniel
categories:
- [ Programming, .NET, F# ]
- [ Series, A Tour of myPrayerJournal ]
tags:
- api
- composition
- configuration
- entity
- f#
- fish
- get
- giraffe
- handler
- json
- kestrel
- operator
- patch
- post
- rest
- router
- routing
- scott wlaschin
- spa
- suave
---
_NOTES:_
- _This is post 4 in a series; see [the introduction][intro] for all of them, and the requirements for which this software was built._
- _Links that start with the text "mpj:" are links to the 1.0.0 tag (1.0 release) of myPrayerJournal, unless otherwise noted._

Now that we have a wonderful, shiny, reactive front end, we need to be able to get some data into it. We'll be communicating via <abbr title="JavaScript Object Notation">JSON</abbr> between the app and the server. In this post, we'll also attempt to explain some about the F# language features used as part of the API.

## The Data

The entities are defined in Data.fs ([mpj:Data.fs][Data.fs]). We'll dig into them more fully in the upcoming "data store" post, but for now, we'll just focus on the types and fields. We have four types: `History` (lines 33-39), `Note` (lines 67-71), `Request` (lines 94-110), and `JournalRequest` (lines 153-173). A `Request` can have multiple `Note`s and `History` entries, and `JournalRequest` is based on a view that pulls these together and computes things like the current text of the request and when it should be displayed again.

We apply no special JSON transformations, so the fields in these record types are the properties in the exported JSON.

## The URLs

To set the API apart from the rest of the URLs, they all start with `/api`. Request URLs generally follow the form `/request/[id]/[action]`, and there is a separate URL for the journal. Line 54 in `Program.fs` ([mpj:Program.fs][Program.fs]) has the definition of the routes. We used [Giraffe][]'s [Token Router][TR] instead of the traditional one, as we didn't need to support any URL schemes it doesn't. The result really looks like a nice, clean "table of contents" for the routes support by the API. _(While we tried to follow <abbr title="Representational State Transfer">REST</abbr> principles in large part, the REST purists would probably say that it's not quite RESTful enough to claim the name. But, hey, we do use `PATCH`, so maybe we'll get partial credit.)_

We aren't done with routes just yet, though. Let's take a look at that `notFound` handler ([mpj:Handlers.fs][Handlers.fs]); it's on line 27. Since we're serving a <abbr title="Single Page Application">SPA</abbr>, we need to return `index.html`, the entry point of the SPA, for URLs that belong to it. Picture a user sitting at `https://prayerjournal.me/journal` and pressing "Refresh;" we don't want to return a 404! Since the app has a finite set of URL prefixes, we'll check to see if one of those is the URL. If it is, we send the Vue app; if not, we send a 404 response. This way, we can return true 404 responses for the inevitable hacking attempts we'll receive (pro tip, hackers - `/wp-admin/wp-upload.php` does not exist).

## Defining the Handlers

Giraffe uses the term "handler" to define a function that handles a request. Handlers have the signature `HttpFunc -> HttpContext -> Task<HttpContext option>` (aliased as `HttpHandler`), and can be composed via the `>=>` ("fish") operator. The `option` part in the signature is the key in composing handler functions. The `>=>` operator creates a pipeline that sends the output of one function into the input of another; however, if a function fails to return a `Some` option for the `HttpContext` parameter, it short-circuits the remaining logic.<a href="#note-1"><sup>1</sup></a>

The biggest use of that composition in myPrayerJournal is determining if a user is logged in or not. Authorization is also getting its own post, so we'll just focus on the yes/no answer here. The `authorized` handler (line 71) looks for the presence of a user. If it's there, it returns `next ctx`, where `next` is the next `HttpFunc` and `ctx` is the `HttpContext` it received; this results in a `Task<HttpContext option>` which continues to process, hopefully following the happy path and eventually returning `Some`. If the user is not there, though, it returns the `notAuthorized` handler, also passing `next` and `ctx`; however, if we look up to line 67 and the definition of the `notAuthorized` handler, we see that it ignores both `next` and `ctx`, and returns `None`. However, notice that this handler has some fish composition in it; `setStatusCode` returns `Some` - it has succeeded - but we short-circuit the pipeline immediately thereafter.

We can see this in use in the handler for the `/api/journal` endpoint, starting on line 137. Both `authorize` and the inline function below it have the `HttpHandler` signature, so we can compose them with the `>=>` operator. If a user is signed in, they get a journal; if not, they get a 403.

When a handler is expecting a parameter from a route, the handler's signature is a bit different. The handler for `/api/request/[id]`, on line 246, has an extra parameter, `reqId`. If we look back in `Program.fs` line 64, we see where this handler is assigned its route; and, if you compare it to the route for `/api/journal` on line 59, you'll see that it looks the same. The difference here is the expectations of `route` (for the journal) vs. `routef` (for the request). `route` expects no parameter, but `routef` will extract the parameters, `Printf` style, and pass them as parameters that precede the `HttpHandler` signature.

## Executing the Handlers

myPrayerJournal has `GET`, `POST`, and `PATCH` routes defined. We'll look at representative examples of each of these over the next few paragraphs.

For our `GET` example, let's return to the `Request.get` handler, starting on line 246. Once we clear the `authorize` hurdle, the code attempts to retrieve a `JournalRequest` by the request ID passed to the handler and the user ID passed as part of the request. If it exists, we return the JSON representation of it; if not, we return a 404. Note the order of parameters to the `json` result handler - it's `json [object] next ctx` (or `json [object] HttpHandler`). We also defined an `asJson` function on line 75; this flips the parameters so that the output object is the last parameter (`asJson next ctx [object]`), enabling the flow seen in the `journal` handler on line 137.

For our `POST` example, we'll use `Request.addNote`, starting on line 218. It checks authorization, and to make sure the request exists for the current user. If everything is as it should be, it then uses Giraffe's `BindJsonAsync<'T>` extension method to create an instance of the expected input form. Since the handler doesn't have a place to specify the expected input object, this type of model binding cannot happen automatically; the upside is, you don't waste CPU cycles trying to do it if you don't need it. Once we have our model bound, we create a new `Note`, add it, then return a 201 response.

`PATCH` handlers are very similar; `Request.snooze` is one such handler, starting on line 291. The flow is very similar as the one for `Request.addNote`, except that we're updating instead of adding, and we return 204 instead of 201.

## Configuring the Web Server

Giraffe enables [Suave][]-like function composition on top of Kestrel and the ASP.NET Core infrastructure. Rather than using the `Startup` class, myPrayerJournal uses a functional configuration strategy. The calls are in `Program.fs` starting on line 115; there are lots of other guides on how to configure ASP.NET Core, so I won't say too much more about it.

Notice, though, line 31. Earlier, we discussed the `>=>` operator and how it worked. This is an example of the `>>` composition operator, which is part of F#. For functions whose output can be run directly into the next function's input, the `>>` operator allows those functions to be composed into a single function. If we were to look at the signature of the composed function within the parentheses, its signature would be `string -> unit`; so, we pass the string "Kestrel" to it, and it runs through and returns `unit`, which is what we expect for a configuration function. Here's how that breaks down:
- `ctx.Configuration.GetSection`'s signature is `string -> IConfigurationSection`
- `opts.Configure`'s signature is `IConfiguration -> KestrelConfigurationLoader` (`IConfigurationSection` implements `IConfiguration`)
- `ignore`'s signature is `'a -> unit`

If this still doesn't make sense, perhaps this will help. The `Configure.kestrel` function could also have been written using the `|>` piping operator instead, with the equivalent code looking like:

{% codeblock lang:fsharp %}
  let kestrel (ctx : WebHostBuilderContext) (opts : KestrelServerOptions) =
    ctx.Configuration.GetSection "Kestrel"
    |> opts.Configure
    |> ignore
{% endcodeblock %}

<p>&nbsp;</p>

That concludes our tour of the API for now, though we'll be looking at it again next time, when we take a deeper dive into authentication and authorization using Auth0.

---

<a name="note-1"><sup>1</sup></a> _Scott Wlaschin has a great post entitled ["Railway Oriented Programming"][ROP] that explains this concept in general, and [the fish operator][ROP-fish] specifically. Translating his definition to Giraffe's handlers, the first function is `switch1`, the `next` parameter is `switch2`, and the `HttpContext` is the `x` parameter; instead of `Success` and `Failure`, the return type utilizes the either/or nature of an option being `Some` or `None`. If you want to understand what makes F# such a great programming model, you'll spend more time on his site than on The Bit Badger Blog._


[intro]: /2018/a-tour-of-myprayerjournal/introduction.html "A Tour of myPrayerJournal: Introduction | The Bit Badger Blog"
[Data.fs]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Data.fs "api/Data.fs | myPrayerJournal | GitHub"
[Program.fs]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Program.fs "api/Program.fs | myPrayerJournal | GitHub"
[Giraffe]: https://github.com/giraffe-fsharp/Giraffe "Giraffe | GitHub"
[TR]: https://github.com/giraffe-fsharp/Giraffe.TokenRouter "Giraffe.TokenRouter | GitHub"
[Handlers.fs]: https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Handlers.fs "app/Handlers.fs | myPrayerJournal | GitHub"
[Suave]: https://suave.io
[ROP]: https://fsharpforfunandprofit.com/posts/recipe-part2/ "Railway oriented programming | F# for Fun and Profit"
[ROP-fish]: https://fsharpforfunandprofit.com/posts/recipe-part2/#an-alternative-to-bind "An alternative to bind | Railway oriented programming | F# for Fun and Profit"
