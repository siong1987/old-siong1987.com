---
layout: post
title: Serendipity and the Square
description: Why Instructors Should Never Use Piazza From the Coffee Shop.
---

Piazza initially uses HTTPS to authenticate users, but afterward, HTTP is used for requests.

Furthermore, each request appears to contain an easily-identified Piazza session cookie.  As the requests are not encrypted, this cookie can be obtained by a third party observing on a WiFi network without encryption.

## How Session Spoofing Could Be Done

We noticed that there was no “HTTPS” browser indicator after we had logged onto Piazza.  We wanted to see if we could intentionally share session cookies between ourselves.  We discovered that not only would the requests be useful for a replay attack, but using another user’s cookie actually fools Piazza into misidentifying an authenticated user.
  
Siong was able to log onto Piazza as Derek using the session cookie of Derek.

We did so this way:

* Derek browsed to piazza.com, which redirects to https://piazza.com/
* Derek logged on normally to Piazza and arrived at http://piazza.com/class#spring2012/cs461ece422
* Derek clicked the favicon in the browser’s navigation bar:


