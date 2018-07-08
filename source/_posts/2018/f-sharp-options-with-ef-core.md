---
layout: post
title: F# Options with EF Core
date: 2018-07-08 17:00:00
author: Daniel
categories:
- [ Programming, .NET, F# ]
tags:
- f#
- ef core
- entity framework core
---
The 2.1 release of [Entity Framework Core][efcore] brought the ability to do [value conversions][vc]. This is implemented through an abstract class, `ValueConverter`, which you can implement to convert a data type. They also provided [several built-in converters][bic] that you don't have to write, such as storing `enum`s as strings. To use a value converter, you provide a new instance of it and attach it to a property in your model's `OnModelCreating` event.

F# provides an `Option<'T>` type as a way to represent a value that may or may not be present. There are many benefits to defining optional values as `'T option` rather than checking for null; you can [read all about it][opt] if you'd like.

As I was working on a project, I already used `Option.ofObj` to convert my possibly-null results from queries to options; at the field level, though, I was working with default values. Could I use this new feature to handle `null`able columns as well? As it turns out, yes!

Here is the code for the value converter.

{% codeblock lang:fsharp %}
module Conversion =
  
  open Microsoft.FSharp.Linq.RuntimeHelpers
  open System
  open System.Linq.Expressions

  let asLinqExpr<'T> = (LeafExpressionConverter.QuotationToExpression >> unbox<Expression<Func<'T, 'T option>>>)

  let toOption<'T> =
    <@ Func<'T, 'T option>(fun (x : 'T) -> match box x with null -> None | _ -> Some x) @>
    |> asLinqExpr
  
  let fromOption<'T> =
    <@ Func<'T option, 'T>(fun (x : 'T option) -> match x with Some y -> y | None -> Unchecked.defaultof<'T>) @>
    |> asLinqExpr

type OptionConverter<'T> () =
  inherit ValueConverter<'T option, 'T> (Conversion.fromOption, Conversion.toOption)
{% endcodeblock %}

The `Conversion` module contains the functions that we'll need to provide in the `ValueConverter` constructor. _(With the way class inheritance is coded in F#, and the way `ValueConverter` wants its expressions in its constructor, this is a necessary step. I would have liked to have seen a no-argument constructor and overridable properties as an option, but I'm not complaining; this is a really great feature.)_ Within those functions, we make use of [code quotations][quot], then convert the quotation expressions to Linq expressions.

One other note; in the `toOption` function, if we used `Option.ofObj` instead of `box x`, the code would not support value types. This means that things like an `int option` field wouldn't be supported.

Now that we have our option converter, let's hook it into our model. In my project, each entity type has a static `configureEF` function, and I call those from `OnModelCreating`. Here's an abridged version of one of my entity types:

{% codeblock lang:fsharp %}
  [<CLIMutable>]
  [<NoEquality>]
  [<NoComparison>]
  Member =
  { /// ...
    /// E-mail format
    format : string option
    /// ...
    }
  with
    /// ...
    static member configureEF (mb : ModelBuilder) =
      /// ... HasColumnName statements, etc.
      mb.Model.FindEntityType(typeof<Member>).FindProperty("format").SetValueConverter(OptionConverter<string> ())
      |> ignore
{% endcodeblock %}

This line of code finds the type within the model, the property within the type, and provides the new instance of our option converter to it. In this entity, a `None` here indicates that the member uses the group's default e-mail format; `Some` would indicate that they've specified which format they prefer.

That's all there is to it! Define the coverter once, and plug it in to all the optional fields; now we have nullable fields translated to options by EF Core. ["Magic unicorn,"][mu] indeed!

_(Credits: Many thanks to Jiří Činčura for the [excellent value conversion blog post][vcblog] and Tomas Petricek for his [Stack Overflow answer on converting quotation expressions to Linq expressions][so].)_

[efcore]: https://docs.microsoft.com/en-us/ef/core/
[vc]: https://docs.microsoft.com/en-us/ef/core/modeling/value-conversions
[bic]: https://docs.microsoft.com/en-us/ef/core/modeling/value-conversions#built-in-converters
[opt]: https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/options
[quot]: https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/code-quotations
[mu]: https://twitter.com/efmagicunicorns
[vcblog]: https://www.tabsoverspaces.com/233708-using-value-converter-for-custom-encryption-of-field-on-entity-framework-core-2-1
[so]: https://stackoverflow.com/a/23146624
