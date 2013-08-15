---
layout: post
title: "Async Unique validation with expressjs and mongoose"
date: 2013-08-06 13:02
comments: true
categories: 
---

In a nutshell: here is how to add elegant validation with a useful error message for a field being unique. This is great for usernames, email addresses etc. that can only appear once in your collection. 

Line 16 onwards  are the ones to note. It's a custom validation rule set on "email". What's important is that it adds a callback to the validate function which I have arbitrarily named `respond`. Notice that I can now search for an existing user and in the callback for that search, if a user is found, I can return false to my validation by passing false through my `respond` callback.

{% gist 6163577 %}
