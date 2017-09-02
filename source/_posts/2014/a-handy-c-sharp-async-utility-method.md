---
layout: post
title: A Handy C# Async Utility Method
author: Daniel
date: 2014-08-04 19:48:43
categories:
- [ Programming, .NET, C# ]
tags:
- asynchronous
- c#
- utility
summary: Using async when you can't use async
---

In the course of writing C# code utilizing the new (for 4.5.1) [Task-based asynchronous programming][async], I've run across a couple of places where the `await` keyword either is not allowed (a catch block or a property accessor) or the `async` keyword greatly complicates the syntax (lambda expressions). I've found myself writing this method for two different projects, and so I thought I would drop this Q&D, more-comments-than-code utility method here for others to use if you see the need.

_(UPDATE: This works well in console applications; it can cause deadlocks in desktop and web apps. Test before you rely on it.)_

{% codeblock lang:csharp %}
/// <summary>
/// Get the result of a task in contexts where the "await" keyword may be prohibited
/// </summary>
/// <typeparam name="T">The return type for the task</typeparam>
/// <param name="task">The task to be awaited</param>
/// <returns>The result of the task</returns>
public static T TaskResult<T>(Task<T> task)
{
    Task.WaitAll(task);
    return task.Result;
}
{% endcodeblock %}

And, in places where you can't do something like this...

{% codeblock ExampleClass.cs lang:csharp %}
/// <summary>
/// A horribly contrived example class
/// </summary>
/// <remarks>Don't ever structure your POCOs this way, unless EF is handling the navigation properties</remarks>
public class ExampleClass
{
    /// <summary>
    /// A contrived ID to a dependent entity
    /// </summary>
    public int ForeignKeyID { get; set; }

    /// <summary>
    /// The contrived dependent entity
    /// </summary>
    public DependentEntity DependentEntity
    {
        get
        {
            // Does not compile; can't use await without async, can't mark a property as async
            return await Data.DependentEntities
                .FirstOrDefaultAsync(entity => entity.ID == ForeignKeyID);
        }
    }
}
{% endcodeblock %}

...you can instead do this in that "DependentEntity" property...

{% codeblock lang:csharp %}
    /// <summary>
    /// The contrived dependent entity
    /// </summary>
    public DependentEntity DependentEntity
    {
        get
        {
            return UtilClass.TaskResult<DependentEntity>(Data.DependentEntities
                .FirstOrDefaultAsync(entity => entity.ID == ForeignKeyID));
        }
    }
{% endcodeblock %}


[async]: //msdn.microsoft.com/EN-US/library/vstudio/hh191443(v=vs.110).aspx "Asynchronous Programming with Async and Await - MSDN"
