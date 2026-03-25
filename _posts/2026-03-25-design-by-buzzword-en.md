---
layout: post
title: "Design by Buzzword"
date: 2026-03-25 00:00:00 +0900
comments: true
categories: ["blog"]
tags:
 - architecture
 - design

image: /images/2026-03-25-design-by-buzzword/software_legends.jpg
---

<figure style="margin-bottom: 2.5em">
<img src="/images/2026-03-25-design-by-buzzword/software_legends.jpg" alt="Those who posed the questions">
<figcaption><em>Those who posed the questions — OOP (Alan Kay), MVC (Trygve Reenskaug), DDD (Eric Evans), Agile, DRY (Dave Thomas), CQRS (Greg Young), DevOps (Patrick Debois)</em></figcaption>
</figure>

In 2000, Roy T. Fielding submitted his doctoral dissertation to the University of California, Irvine. On the very first page of Chapter 1—the first thing a reader sees—he quoted a Monty Python sketch, *The Architects Sketch*.

```text
Excuse me... did you say "knives"?
— City Gent #1 (Michael Palin), 'The Architects Sketch' [111]
```

I didn't understand this reference for a long time. Why would a REST dissertation open with Monty Python?

## The Slaughterhouse Architect

An architect who has only ever designed slaughterhouses is commissioned to design an apartment block. He designs it as a slaughterhouse. He proudly walks the tenants through rooms fitted with rotating knives.

I came to understand this as a warning against applying software architectural approaches without regard for the problem at hand. (Does "microservices" come to mind?)

And right after the sketch, Fielding writes: "design-by-buzzword is a common occurrence."

I wrote in detail about how REST became a buzzword in a [previous article](https://koriym.github.io/blog/2026/03/03/rest-how-it-became-buzzword-en). Trace the history, and a pattern emerges: the original "question" disappears, the name circulates as an empty vessel of authority, the active ingredient is stripped out, an ecosystem is built to compensate for the loss, and the next buzzword arrives to "solve" the problem.

This is not just a REST story. Design-by-buzzword has a pattern that has repeated for over half a century.

## The Anatomy of the Pattern

1. It begins with a "question" about a problem.
2. The WHAT of the question becomes the HOW of practice, and spreads.
3. The name becomes a vessel of authority, and degradation—with the core stripped out—advances with conviction.
4. The active ingredient is dropped, and an ecosystem is built to compensate for the loss.
5. The next buzzword "solves" it.

This pattern has repeated for half a century. Sometimes the popularized version creates a "pragmatic vs. dogmatic" frame that seals off any attempt at correction.

## OOP

In 1967, Alan Kay encountered Simula, a Norwegian programming language. The seeds of the object concept were there. But what excited Kay wasn't objects themselves—it was the vision beyond them. Could software be designed as a collection of autonomous cells, like biological cells that function independently yet work together as a whole?

At the heart of Kay's question was message passing. Just as cells communicate by exchanging chemical signals, objects cooperate by sending messages to each other. Internal state is hidden; from the outside, only messages arrive. Not a separation of data and procedures, but designing systems as communication networks of autonomous agents—that was what Smalltalk set out to achieve.

But as "object-oriented" spread, message passing disappeared. Classes and inheritance were promoted to "the three pillars of OOP," and three words—encapsulation, inheritance, polymorphism—lined up in textbooks. The concept Kay valued most became the most overlooked premise.

What happened as a result? Deep inheritance hierarchies, god classes, tight coupling. Frustration accumulated that "OOP is too complex," and functional programming gained attention as an alternative where those problems didn't arise. But the communication network of autonomous agents that Kay had tried to build in Smalltalk was reinvented as the actor model in the functional context. Erlang in 1986, Akka in 2009. What Kay had intentionally placed at the center was reimplemented under a different name.

Kay wrote in a 1998 [email](https://lists.squeakfoundation.org/pipermail/squeak-dev/1998-October/017019.html):

> "I'm sorry that I long ago coined the term 'objects' for this topic because it gets many people to focus on the lesser idea. The big idea is messaging."

Remember the structure of this statement. It will come up again.

## MVC

In 1978, Trygve Reenskaug was visiting Xerox PARC. The question he was grappling with was simple: "How do you translate the worldview inside a user's head into code?"

When users interact with an application, they carry their own mental model. An accountant using a spreadsheet has a world of cells and formulas in their head. How well that mental model matches the system determines usability. Reenskaug defined Model as the user's mental model itself. Controller was the translator between user and system. View was its representation.

The division into three components was not for organizing code. It was a structure designed to answer the question: "How do we faithfully capture the user's worldview?"

But as MVC spread through the web, the question disappeared. It became a classification problem: "Which of three layers does this code go in?" Model became the place for database access, Controller became the place for handling HTTP requests, View became the place for outputting HTML. The concept of the user's mental model vanished from every textbook.

The Fat Controller debate and the Fat Model debate began. Different frameworks gave different answers to "where should logic go?" Web MVC and GUI MVC were different things called by the same name, and MVP, MVVM, and MVI emerged from entirely different contexts. Only the name was shared; the substance was different.

The question Reenskaug asked—"Are we capturing the user's mental model?"—no one cares about anymore. Twenty-five years later, Evans would pose the same question from a different angle.

## DDD

In 2003, Eric Evans published "Domain-Driven Design." A thick book of 550 pages. It contained two kinds of patterns: tactical patterns like Entity, ValueObject, Repository, and Aggregate; and strategic patterns centered on Ubiquitous Language and Bounded Context. What Evans truly wanted to convey was the latter.

Evans's question was: "How do you accurately translate complex domain logic into software?" The core of his answer lay in developers and domain experts speaking the same language (Ubiquitous Language) and consciously drawing boundaries around models (Bounded Context). Repository was merely an implementation tool.

But as DDD spread, the strategic patterns disappeared. Tactical patterns were concrete and easy to turn into code. Strategic patterns were abstract and highly context-dependent. "Adopting DDD" became creating Entity classes and Repository classes.

With the thinking of drawing boundaries through Bounded Context stripped away, systems without boundaries proliferated. Frustration accumulated: "DDD just adds more code," "It's over-engineering."

Evans later said: "I should have made strategic design more prominent." The microservices story demonstrates this.

## Agile

In February 2001, seventeen people gathered at a ski resort in Snowbird. What they shared was a single anger: "Waterfall, unable to respond to change, is preventing us from delivering working software." Two days of discussion produced the "Manifesto for Agile Software Development"—four values and twelve principles.

The first value reads: "Individuals and interactions over processes and tools."

The manifesto was over-compressed. Published in the extremely short form of four values and twelve principles, what circulated was not a copy of a copy but the copy itself. The Scrum framework became its vessel. Sprints, daily standups, burndown charts—the rituals became "Agile."

"Individuals and interactions over processes and tools" was implemented **by process**.

The industrialization was faster and bigger than REST or DDD. The profession of "Agile Coach" was born. SAFe (Scaled Agile Framework)—a heavyweight process that Agile was supposed to reject—was sold as "Enterprise Agile." A mechanism for coaching critics as "not understanding Agile" was built into the ecosystem. The more you try to correct, the stronger the fundamentalist label grows.

Signatory Dave Thomas wrote on his blog in 2014: "Agile is Dead (Long Live Agility)"—"Stop using the word Agile as a noun." Nouns can be sold. Certified. Consulted. Adjectives cannot.

What Thomas offered instead wasn't even a manifesto. "Figure out where you are. Take a small step towards your goal. Adjust your understanding based on what you learned. Repeat." A mere epistemological attitude.

## CQRS

Greg Young emerged from the DDD community. His starting point was a simple observation: the optimal model differs between reads and writes.

Writes need complex models to protect domain integrity. Reads want denormalized, flat data for screen display. Trying to satisfy both with the same model creates strain. Just separate them—that was all there was to it.

The moment the name "Command Query Responsibility Segregation" was attached, misunderstanding began. Command is business decision-making; Query is structure for display. These were the two things Young wanted to separate. But it was read as "an architecture that physically separates commands and queries." Separate the databases. Separate the infrastructure. Young kept saying "it's simply the creation of two objects where there was previously only one." The same trajectory as Fielding.

Then Event Sourcing was bound to it by equation. "If you're doing CQRS, you need Event Sourcing." Young himself stated clearly that CQRS and Event Sourcing were independent concepts, but it didn't reach anyone.

Adopting CQRS required Event Sourcing, which required distributed messaging, which required the Saga pattern. A chain where each misuse demands the next. At each stage, people are "working hard," so there's no room to verify whether they're actually answering the original question.

"CQRS is too complex," the criticism went. But most of that complexity came not from CQRS itself but from unnecessary coupling. Complexity born from misuse became criticism of the concept itself.

Young kept saying: "Most systems don't need Event Sourcing."

## Microservices

In the 2000s, Amazon and Netflix faced different crises. Amazon had a monolith constraining development speed; Netflix experienced total service outage from a data center failure. But what they were grappling with was an organizational design question before it was a technical one: "How do we create a structure where teams can decide independently and deploy independently?"

Amazon's "two-pizza rule" was about team size. Netflix's "Chaos Monkey" was about embedding resilience into the organization. Service decomposition was a means, not an end.

But as the name "microservices" spread, the organizational design question disappeared. It became an architectural pattern: "split services small." Companies launched "microservices migration projects" that split systems without changing organizations.

Services were split, but boundaries were arbitrary. With the question of Bounded Context dropped, Service A knew the internal implementation of Service B. Deploys were supposed to be independent, yet changing one broke others. Tight coupling over the network—a "Distributed Monolith" emerged. The worst of both worlds: the complexity of a monolith and the operational cost of microservices.

The "solution" that appeared was "Modular Monolith." A monolith with proper boundaries. Isn't that a reinvention of "Bounded Context," which Evans wrote about in 2003?

After twenty years and one massive failure, we returned to the starting point.

Martin Fowler wrote his microservices article in 2014, then wrote "Microservice Prerequisites" in 2015. If the organization isn't ready, microservices produce worse results than a monolith. It was too late.

## DevOps

In 2008, Patrick Debois and Andrew Shafer were talking in the hallway at an Agile conference. What they faced wasn't a technical problem. It was an organizational fracture: "The development team ships it, and the operations team receives it broken." Tearing down that wall was the question.

A name that simply joined "Dev" and "Ops" could be applied to everything in software development and operations. Like REST and Agile, its coverage area was maximal.

But the question of organizational culture transformation disappeared. CI/CD pipelines, Kubernetes, Infrastructure as Code—adopting toolchains became "adopting DevOps."

The structure of industrialization differed from Agile. Agile's industrialization was driven by consultants and training; DevOps was driven by cloud vendors. AWS defined DevOps, Azure certified DevOps, GCP sold DevOps. The equation "DevOps requires this tool" translated directly to revenue. When the entity driving industrialization has built the concept's misuse into its business model, correction becomes nearly impossible. Fielding can push back on a blog, but you can't push back against an AWS whitepaper.

The supreme irony: a concept meant to dissolve the fracture between Dev and Ops spawned Platform Engineering teams—a new silo. A fracture created to resolve a fracture.

## Long Shot: Comedy

Tragedy and comedy coexist. An individual developer wrestling with Event Sourcing schema migrations is, in close-up, a tragedy. Pull back, and it's a comedy: "someone is implementing with full effort what Greg Young kept saying wasn't necessary."

The organization that implemented "individuals and interactions over processes" by process. DevOps, which created a new fracture to resolve a fracture. Microservices, which took twenty years to reinvent Bounded Context. In close-up, tragedy. In long shot, comedy.

## Fielding Knew

Remember the slaughterhouse architect from the opening.

Every case we've read fits into that single line. Dropping Kay's message passing and taking classes and inheritance. Dropping Reenskaug's mental model and taking three-layer separation. Dropping Evans's strategic design and taking tactical patterns. The industry became the slaughterhouse architect, designing slaughterhouses under a different name each time.

Fielding placed this warning before the body of his dissertation. The very first thing you see. And readers read the warning and misused the dissertation exactly as warned.

Do you remember the structure of Kay's statement? The lament that "a word I coined spread with a meaning I never intended." Evans, Thomas, Young, Fowler—they all made statements with the same structure. Originators trying to correct the misuse of their own concepts, unable to reach anyone.

Fielding was no different. In 2021, he wrote in a tweet thread—with the caveat that it might be the vaccine booster side effects talking:

> When will people realize that the reason they are paid, the reason they are respected for good work, is because they can **use their own minds to fill in the details appropriate to the context of their own systems**.

That caveat is heartbreaking.

Those who can understand a warning never needed it in the first place. Those who need the warning cannot understand it.

Fielding wrote a warning at the very beginning of his REST dissertation. That warning—**REST**—became the greatest source of misuse. He watched it for twenty-five years.

## Why Nothing Changes

---

399 BC, Athens.

In the democracy, rhetorical ability was directly tied to power. Meeting that demand were the Sophists—professional rhetoricians who taught "the art of arguing any position persuasively" in exchange for payment. Socrates kept asking: "What is the question that this answer is answering?" His dialogues, which led young people to realize "I know nothing," were seen as threatening to the established order. He was executed for "impiety and corrupting the youth." Meanwhile, the Sophists' techniques—which carried his voice—were systematized and passed down to posterity. **The power of those who transmit is stronger than those who pose the questions. People listen to those who sell the how, not those who posed the what.**

The industry is waiting for the next buzzword.

---

## References

- [Roy T. Fielding, "Architectural Styles and the Design of Network-based Software Architectures", Chapter 1](https://roy.gbiv.com/pubs/dissertation/introduction.htm)
- [The Architects Sketch - Monty Python's Flying Circus](https://youtu.be/ByzFlH2e2Js?si=3sOIpZ71pZ9DSBID)
- [Manifesto for Agile Software Development](https://agilemanifesto.org/)
- [Two Worldviews on CQRS](https://qiita.com/koriym/items/4e52758d5327447fed13) (Japanese)
- [Roy Fielding, Twitter thread, 2021](https://x.com/fielding/status/1458499370672791565)

### The Laments of the Originators

- [OOP](#oop): Alan Kay, 1998: ["The big idea is messaging"](https://lists.squeakfoundation.org/pipermail/squeak-dev/1998-October/017019.html) — "I'm sorry that I long ago coined the term 'objects'..."
- [DDD](#ddd): Eric Evans, 2009: ["What I've Learned about DDD since the Book"](https://www.youtube.com/watch?v=lE6Hxz4yomA) — "Why did I put Context Mapping in Chapter 14?"
- [Agile](#agile): Dave Thomas, 2014: ["Agile is Dead (Long Live Agility)"](https://pragdave.me/thoughts/active/2014-03-04-time-to-kill-agile.html) — "Stop using the word Agile as a noun"
- [Microservices](#microservices): Martin Fowler, 2015: ["Microservice Prerequisites"](https://martinfowler.com/bliki/MicroservicePrerequisites.html) — "you must be this tall to use microservices"
- [REST](https://koriym.github.io/blog/2026/03/03/rest-how-it-became-buzzword-en): Roy Fielding, 2008: ["REST APIs must be hypertext-driven"](https://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven) — "I am getting frustrated..."
