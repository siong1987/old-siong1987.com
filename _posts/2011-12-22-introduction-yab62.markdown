---
layout: post
title: Introducing YAB62
description: Yet Another Base 62 Convertor
---

Link shortener is fun and easy project that every web programmer must
have learned how to create one before. Basically, the easiest way to create
a link shortener is to have a database table which has an incremental
integer key and matches that key to the original url.

## Base 62

But, base 10 incremental integer key might not help to shorten your url
too much(less characters). Hence, a lot of link shortener instead uses a
base 62 key or converts the base 10 key to a base 62 key. Base 62
includes the characters a to z, A to Z and 0 to 9.

