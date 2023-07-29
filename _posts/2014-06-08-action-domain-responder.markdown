---
layout: post
title: "Action-Domain-Responder"
date: 2014-06-08 09:50
comments: true
categories: articles
tags:
- PHP
---

<img src="left  https://raw.githubusercontent.com/pmjones/mvc-refinement/master/adr.png">
<br clear=all>
https://github.com/pmjones/mvc-refinement

Here is my random thought about [ADR](https://github.com/pmjones/mvc-refinement).

It seems the each component seems to have their own each distinct and meaningful role. Devs will follow a more natural workflow with it. Domains become first class citizens compared to a “data provider” to their GOD controller in some MVC framework.

 I like the fact that an action returns a responder (not actually invoking the action). Data can then be retrieved and tested. Actions can call other actions creating a hierarchical information structure  for which MVC stuggles to provide a solution. Testing and gaining 100% code coverage also seems much easier without using a web driver.

I don’t see any particular problem in having  “More classes in the application”. I think it can gain more benefit by having a smaller amount of LOC per class. It seems people can move from MVC to ADR with ease and gain the benefits instantly.

I would like to add a few personal slightly biased comments, (Just casual remarks - feel free to dismiss.)

In most MVC application frameworks, the existing system (architecture) is mapped to the web. CRUD and OOP paradigms are mapped to HTTP methods and resources. But the opposite is not true. ADR seems to have the same limitation. I would like to echo @nateabele’s tweet “resources aren't like controllers, in that they're intended to be more cohesive”.

I also wonder if I can inject arguments, not “retain incoming data” to an Action, actions can then be an easier to utilize by another Action?

A few questions about the Responder ? “the Action would grab a Domain and represent it through a Responder.” Instead of an Action interacting with the Responder, how about have the Responder look at the Action’s data and use that ? In doing this Action is completely ignorant of the Responder?  Or am I misunderstanding something ?

Conclusion:

To discover the next generation web application framework, grasping a new application architecture seems to be crucial, rather than just a gradual improvement of the MVC pattern. I think it is exciting that an influential person like Paul M. Jones (my PHP hero) is pushing these boundaries somewhat. ADR can be a good replacement of MVC pattern, especially for those who have used an MVC framework but also looks forward a little what might be next. I like it.


English proofreading: [Richard McIntyre](https://twitter.com/mackstar)

(Arigato as always :)
