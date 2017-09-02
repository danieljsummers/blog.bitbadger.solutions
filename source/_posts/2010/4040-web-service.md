---
layout: post
title: 40/40 Web Service
author: Daniel
date: 2010-09-19 19:55:34
categories:
- [ Programming, Web Services ]
tags:
- erlc
- prayer
- sbc
- web service
summary: A web service to support the 2010 40/40 Prayer Vigil
---

The [Ethics and Religious Liberty Commission][erlc] of the [Southern Baptist Convention][sbc] is holding a "[40/40 Prayer Vigil][site]," encouraging prayer through the end of October. While some of the prayer is focused on the upcoming elections, the focus is on national revival. They have produced a prayer guide, which details suggestions for prayer over the course of 40 days, beginning September 20th, and for 40 hours, beginning October 29th at 4pm.

We have created a web service to break this guide up into day and hour-sized chunks. The service <del>is</del> _(UPDATE: was)_ at http://services.djs-consulting.com/FortyForty.asmx. There are several ways to retrieve this information.

* **GetDay**
  This gets one of the 40 days, by the day number. (September 20th is 1, September 21st is 2, etc.) The "day" parameter controls which day is returned.
* **GetHour**
  This gets one of the 40 hours, by the hour number (10/29 4pm is 1, 10/29 5pm is 2, etc.) The "hour" parameter controls which hour is returned.
* **GetDate**
  This gets one of the 40 days, by the current date. The "date" parameter controls which day is returned. (The time portion may be given, but it is ignored.)
* **GetTime**
  This gets one of the 40 hours, by the date/time. The "time" parameter controls which hour is returned.
* **GetDayHTML**, **GetHourHTML**, **GetDateHTML**, and **GetTimeHTML**
  This is the same as the above 4 calls, except what is returned is a formatted block of text that can be displayed on a web page.

In all cases, if the day/hour/date/time does not match a valid value for the vigil, a null is returned.

If you're not interested in consuming the web service, but you'd like to see the suggested prayer each day, the Hoffmantown Prayer site is displaying the days and hours on Mountain Time. This information is on the front page with no login required.

This web service will be discontinued at some point after December 31, 2010.


[erlc]: //erlc.com "The Ethics and Religious Liberty Commission of the Southern Baptist Convention"
[sbc]:  //www.sbc.net "Southern Baptist Convention"
[site]: //www.4040prayer.com "40/40 Prayer Vigil"
