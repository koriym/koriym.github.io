---
layout: post
title: "\"How REST Became a Buzzword\" — The Unwritten"
date: 2026-03-03 09:00:00 +0900
comments: false
categories: ["blog"]
tags:
  - REST
list_title: false
sitemap: false
permalink: /blog/2026/03/03/rest-how-it-became-buzzword-unwritten-en
---

Themes that ["How REST Became a Buzzword"](/blog/2026/03/03/rest-how-it-became-buzzword-en) does not state directly but embeds in its structure and between the lines.

---

## Degradation and Propagation

- Copies of copies degrade. Dissertation → book → blog → tutorial → Stack Overflow → best practice → framework convention → the mind of a junior developer. At each stage, nuance is lost and certainty increases. The least accurate version is held with the greatest conviction
- Framework conventions are the final stage. Write `resources :articles` and routing is auto-generated. You are using what is called REST without knowing it, and what you are using is not REST. A triple ignorance
- The original source is freely available online. It is not behind a paywall. Still, almost nobody reads it. This is not a problem of access—it is a problem of culture
- Of the many figures in this story, only Fielding is the actual designer. DHH was a brilliant marketer, Apigee a best-practice dealer, Richardson the systematizing author, Fowler the authority amplifier

## Destruction of a Concept

- If FIOH had been called "HTTP API," Fielding's "REST" could have survived as a separate concept. By taking the name, the real thing became unsearchable. Misuse kills concepts
- The "State Transfer" in "Representational State Transfer" does not mean "transferring resource state over the network." It means "transitioning application state by following hyperlinks." Even the meaning of the name itself has settled into an understanding that diverges from the original
- SOAP vs REST was a false dichotomy. REST did not defeat SOAP. FIOH won and put on REST's uniform. A match where the victor stole the loser's name

## Logic Traps

- "Pragmatic vs dogmatic" is a self-sealing argument. The moment Apigee positioned itself as "pragmatic," every attempt at correction became evidence of being "dogmatic." The more you push back, the stronger the RESTifarian label sticks. The framing won the argument before it began
- "Because it's convenient" is unfalsifiable. Working software works. It works no matter what you call it. That is precisely why the naming question must be discussed separately from the functionality question
- Deliberate misuse is more serious than accidental misunderstanding. DHH spoke with Fielding directly. Richardson knew his 2007 book was wrong—that is why he rewrote it in 2013. Apigee cited the dissertation and then dismissed it as "dogmatic." They all read it before doing what they did

## Irony and Self-Reference

- The dissertation's core message is "don't use one style for everything." The industry borrowed the dissertation's name and applied one style to everything. Fielding's dissertation became the virus of the very disease it warned against
- Fielding opened Chapter 1 of his dissertation with a Monty Python sketch (The Architects Sketch) about an architect who has only designed slaughterhouses and designs an apartment block as a slaughterhouse. "Excuse me... did you say 'knives'?" That is exactly what happened
- Fielding warned against "design-by-buzzword" in his dissertation. His dissertation became the biggest buzzword
- "Learn, apply, iterate!"—the industry did exactly that. Except the input was copies. They learned copies, applied copies, and iterated on copies. What Fielding meant was "use your own brain to fill in the details for your own system's context"—the polar opposite of a culture that imports best practices wholesale

## Removal of the Active Ingredient

- With HATEOAS, clients follow links rather than hardcoding URLs. The server can change URLs without breaking clients. Versioning is unnecessary. The industry dropped HATEOAS. Clients began hardcoding URLs. Changing a URL breaks things. Hence `/api/v1/`, `/api/v2/`, `/api/v3/`—API versioning became the greatest challenge of "RESTful API design." They took a design principle meant to enable evolution without breakage, removed the constraint that prevents breakage, and now suffer from the inability to evolve. They kept the name of the medicine, removed the active ingredient, and complain that it does not work
- They dropped self-descriptive messages and built OpenAPI as an external specification instead, then erected an entire ecosystem—Swagger UI, code generators, mock servers—to manage that specification. An ecosystem built on top of a dropped constraint, to compensate for the dropped constraint
- The pain is not recognized as pain. For developers who know nothing but CRUD-over-HTTP, artificial problems look like "the natural cost of API development." If you have never seen the real thing, you cannot see the pain. But the real thing's name has been taken
- Living with misuse distorts decisions downstream. People who say "we tried REST and it didn't work, so let's move on" never tried REST. What they tried was CRUD-over-HTTP, and because it bore the name REST, REST itself was perceived to have failed. They walked with the wrong map and reported "there's a cliff ahead on this map." The map is wrong
- During the HTTP/1.1 design process, Fielding rejected the proposed MGET method—batch retrieval of multiple resources—on the grounds that it would compromise proxyability and cacheability. The "why" behind this decision was never transmitted. The inability to fetch multiple resources at once came to look like "a limitation of REST." GraphQL "solved" it—sacrificing proxyability and cacheability in the process. And then set about tackling the cacheability problem all over again, unaware it had been discarded

## Structural Tragedy

- Authority laundering. Fielding's academic authority → Apigee cites it → Google acquires Apigee → "recommended by Google." The type of authority shifts from academic to commercial, and the content shifts too, but the chain of citations makes it look as though academic backing persists
- Fielding's self-blame—"I was the one who decided that wasn't my audience at the time." Because he wrote it as an academic dissertation, it never reached practitioners. Intermediaries (DHH, Richardson, Apigee) filled the gap. But what the intermediaries passed along was a diluted copy
- The Richardson Maturity Model granted an indulgence: "Level 2 is RESTful enough." A maturity model that justifies stopping at immaturity
- Twenty-five years of collective engineering effort have been spent solving artificial problems (versioning, endpoint design, OpenAPI maintenance). What would the Web look like if the industry had truly understood and applied hypermedia? We will never know

## Meta

- The article itself demonstrates the opposite of what it criticizes. It goes back to primary sources rather than copying copies. As methodology, it is the prescription for the disease it diagnoses
- The opening three paragraphs form a funnel of solitude. "Widely misunderstood" → "even those who know this" → "rarer still." With each sentence, the circle of understanding narrows

---

<p style="color: #999; font-size: 0.8em; margin-top: 3em;">
This document was generated from a dialogue between the author and AI.
</p>
