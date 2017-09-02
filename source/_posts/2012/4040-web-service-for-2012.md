---
layout: post
title: 40/40 Web Service for 2012
author: Daniel
date: 2012-06-17 22:21:01
categories:
- [ Programming, Web Services ]
tags:
- '4040'
- api
- hoffmantown
- english
- espa&ntilde;ol
- html
- json
- prayer
- prayertracker
- rest
- soap
- web service
- wsdl
- xml
summary: The 40/40 Prayer Vigil has been updated for 2012, as a REST API supporting both English and Spanish
---

Back in 2010, we wrote a [web service][post] for the [40/40 Prayer Vigil][4040] organized by the [Ethics and Religious Liberty Commission][erlc] of the Southern Baptist Convention. This allowed us to use the content in multiple places. They are doing another vigil this year, but the service we wrote two years ago was not terribly reusable.

This year, we have developed a reusable web service that should hold up for 2014 and beyond. _(Acronym alert - non-programmers skip the next sentence.)_ This one has a <acronym title="Representational State Translation">REST</acronym> <acronym title="Application Programming Interface">API</acronym> instead of <acronym title="Simple Object Access Protocol">SOAP</acronym> and <acronym title="Web Services Definition Language">WSDL</acronym>, and supports <acronym title="Extensible Markup Language">XML</acronym>, <acronym title="JavaScript Object Notation">JSON</acronym>, and <acronym title="HyperText Markup Language">HTML</acronym> output formats. This year, it also supports both English and Espa&ntilde;ol.

The REST API start page <del>is</del> _(UPDATE: was)_ at http://services.djs-consulting.com/FortyForty. The prayer guides require an output format, a language, the Scripture version, whether the guide is for a day or an hour, and the day or hour number. There are lookup transactions for lists of available output formats, languages, and Scripture versions, and lookups for converting a date to a day number and a date/time to an hour number.

There will be a WordPress plug-in shortly that will utilize this to display the current day or hour's prayer guide directly on your blog; we'll make another post when that is available. Also, starting September 26th (the first day of the vigil), it will be available for display with no login required at the Hoffmantown Prayer and [PrayerTracker][pt] websites. Developers, the service is available now; if you want to write code to utilize the service, you've got 3 months to make it work!


[post]: /2010/4040-web-service.html "40/40 Web Service &bull; DJS Consulting Tech Blog"
[4040]: //erlc.com/4040 "40/40 Prayer Vigil &bull; ERLC"
[erlc]: //erlc.com "Ethics and Religious Liberty Commission"
[pt]:   //prayer.djs-consulting.com "PrayerTracker"
