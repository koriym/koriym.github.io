---
layout: post
title: "An interface as an API"
date: 2017-07-28 02:59:49 +0900
comments: true
categories: blog
tags:
---

For extensions, I encourage you to copy entire class and modify it. Does this sound too wild ? Here are my thoughts.

Suppose we make a `protected` method for future modification. Is this a symptom of violating SRP? If we separate the “extensible concern” and “core concern”, why not separate them as extensible dependency objects in order to conserve SRP?

Yes we loose the some convenience. But a true DI system give us more flexibility with ease. Inherited modification needs hard coded relationships. The modifications and the original class become tightly coupled.  In other words, modification of dependency object injection can be done by context and we can then draw the object graph more freely.

Just conform to DIP, seriously….

An ‘unchangeable public methods and no protected method policy’ encourage us to use the *final* keyword. This helps makes the class stable and closed. The @api annotation may then be needed less.

-- moved from http://koriym.tumblr.com/post/66663678471/an-interface-as-an-api (11 Nov 2013)
