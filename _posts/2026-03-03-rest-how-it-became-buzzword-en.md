---
layout: post
title: "How REST Became a Buzzword"
date: 2026-03-03 10:00:00 +0900
comments: false
categories: ["blog"]
tags:
  - REST
permalink: /blog/2026/03/03/rest-how-it-became-buzzword-en
image: /images/2026-03-03-rest-how-it-became-buzzword/roy-t-fielding.jpg
list_title: true
---

<figure class="author-portrait">
<img src="/images/2026-03-03-rest-how-it-became-buzzword/roy-t-fielding.jpg" alt="Roy T. Fielding">
<figcaption>Roy T. Fielding</figcaption>
</figure>

Most developers think of REST APIs as resource-oriented URL design with CRUD operations mapped to HTTP methods—something like `GET /users/123`. In reality, REST is a hypermedia-based style where clients follow links in server responses to transition application state. This is widely misunderstood.

But even those who know this still misunderstand REST as an API design methodology. It is not. REST is a formalization of the design principles behind distributed hypermedia systems—the architecture of HTTP itself.

Articles explaining this distinction are rare. Articles examining *how* the misunderstanding spread are rarer still. This post traces how REST became a buzzword and how the misuse propagated.

---

## Prehistory of REST

### 1996 — HTTP/1.0 (RFC 1945)

Roy T. Fielding co-authored the HTTP/1.0 specification (RFC 1945) with Tim Berners-Lee and Henrik Frystyk Nielsen. Fielding was already involved in developing the Apache HTTP Server, placing him at the intersection of Web infrastructure implementation and specification.

### 1997–1999 — HTTP/1.1 (RFC 2068 → RFC 2616)

Fielding was deeply involved in the design of HTTP/1.1 as a co-author. The initial version was published as RFC 2068 in 1997, and the standard was finalized as RFC 2616 in 1999.

During this process, Fielding made concrete design decisions. For instance, he rejected proposals for MGET and MHEAD methods—batch retrieval operations—on the grounds that they would compromise proxyability and cacheability. Instead, sending multiple requests over persistent connections was adopted instead.

### 2000 — The REST Dissertation

Fielding submitted his doctoral dissertation, "Architectural Styles and the Design of Network-based Software Architectures," to the University of California, Irvine.

This dissertation is the original source of REST.

---

## What the Dissertation Actually Said

What matters here is the motivation. Fielding was not proposing a new API design methodology. He was retroactively systematizing the architectural principles that had guided the HTTP/1.1 design process.

The chronology is the reverse of what most people assume. HTTP was not designed based on the REST dissertation. Rather, Fielding looked back at the design decisions behind HTTP and formalized the principles underlying them as "REST." Theory did not come first—it was extracted from practice.

Furthermore, the dissertation's subject is not even REST itself. The bulk of the work is devoted to a taxonomy of network-based software architectural styles. REST is primarily discussed in a single chapter. Other styles in the taxonomy include Pipe-and-Filter, Layered-Client-Server, and Client-Cache-Stateless-Server. REST is a derived style combining constraints from several of these. Its full formal name would be Uniform-Layered-Code-on-Demand-Client-Cache-Stateless-Server (ULCODC$SS)—unsurprisingly, this name was not adopted.

The dissertation's core message is: "Don't use one architectural style for everything. Select a style that matches the needs of your particular problem." Fielding himself wrote:

> "design-by-buzzword is a common occurrence. ... a good designer should select a style that matches the needs of a particular problem being solved."

And on REST's scope of applicability:

> "REST is designed to be efficient for large-grain hypermedia data transfer, optimizing for the common case of the Web, but resulting in an interface that is not optimal for other forms of architectural interaction."

The problem domain the dissertation addresses is "anarchic scalability"—high-performance document connectivity across organizational and national boundaries. This is the structure of the Web itself, not enterprise backend APIs or mobile app BFFs.

---

## The Uniform Interface

Of the architectural constraints that make up REST, the most central is the Uniform Interface.

Fielding defined this constraint as four sub-constraints in the dissertation:

1. **Identification of Resources** — Every resource is identified by a URI
2. **Manipulation of Resources through Representations** — Resources are manipulated through their representations (JSON, HTML, etc.), not directly
3. **Self-descriptive Messages** — Messages contain (or link to) all information necessary for processing
4. **Hypermedia as the Engine of Application State (HATEOAS)** — What can be done next is communicated through links returned by the server

The fourth constraint, HATEOAS, means that clients discover available actions by following links the server provides. Fielding later stated explicitly that without this, you cannot call it REST.

---

## The SOAP Era and Its Backlash

### Early 2000s — SOAP's Heyday

When the REST dissertation was published in 2000, service integration on the Web was heading in an entirely different direction: SOAP (Simple Object Access Protocol).

SOAP was an XML-based protocol where both requests and responses were wrapped in XML envelopes. Type definitions used WSDL, service discovery used UDDI, security used WS-Security—a tower of related specifications. In the enterprise world, this formality was seen as a hallmark of reliability.

### Mid-2000s — Backlash and the Buzzword-ification of "REST"

However, SOAP's complexity imposed a heavy burden on implementers. XML parsing, code generation from WSDL, namespace collisions—for developers who wanted to build APIs quickly on the Web, SOAP was overkill.

Amid this backlash, references using the word "REST" to describe CRUD mappings appeared as early as 2004. [Matthew J. Dovey](https://www.ceridwen.com/srw/record-update/crud-html/) of the University of Oxford's e-Science Centre published an article that referenced Fielding's dissertation and explicitly stated: "REST maps the CRUD operations onto the HTTP verbs (Create=POST, Retrieve=GET, Update=PUT, Delete=DELETE)."

"Just use HTTP the way it was designed"—within this movement, "REST" began circulating as a counter-cultural buzzword against SOAP. Use HTTP methods and status codes directly. Reflect resource structure in URLs. No WSDL, no UDDI needed. The pragmatic appeal was real: it leveraged the semantics and infrastructure (proxies, caches, load balancers) that HTTP already provided.

But this approach was a far cry from the REST described in Fielding's dissertation. [TwoBitHistory](https://twobithistory.org/2020/06/28/rest.html) (2020) called this **FIOH** (**Fuck It, Overload HTTP**). There is nothing inherently wrong with FIOH. The problem is that what was merely FIOH got dressed up with the academic label "REST."

### 2007 — DHH and Rails

In the Ruby on Rails 2.0 release, SOAP support (ActionWebService) was removed from the defaults. In the [official blog announcement](https://rubyonrails.org/2007/12/7/rails-2-0-it-s-done), DHH (David Heinemeier Hansson) wrote:

> "Rails has picked a side in the SOAP vs REST debate. Unless you absolutely have to use SOAP for integration purposes, we strongly discourage you from doing so."

Simultaneously, Rails introduced RESTful routing as a central framework convention. Write `resources :articles` and the framework would automatically generate mappings between HTTP methods and CRUD operations. DHH called this "RESTful" and used it as a banner against SOAP.

DHH was not ignorant. In a [2007 blog post](https://dhh.dk/posts/12-good-times-at-railsconf-europe), he wrote that "the past year and a half has been spent trying to understand and implement the ideas and specifications that Roy has been involved in for the past decade," and he spoke directly with Fielding at RailsConf Europe. Yet in a [2012 blog post](https://dhh.dk/2012/the-parley-letter.html), he wrote that his motivation for introducing REST was "not for the sake of intellectual purity," asking of design principles: "what have you done for me lately?" He mentioned that he tried hypermedia for the Basecamp API but found "it just didn't do much for me." In [Signal v. Noise](https://signalvnoise.com/posts/3373-getting-hyper-about-hypermedia-apis) that same year, he went further, calling hypermedia APIs "completely overblown."

He knew Fielding's work, spoke with the man himself, tried HATEOAS, and decided "I don't need this." This was not a misunderstanding—it was a choice. Choosing the name "REST" served, in my view, as marketing—a banner against SOAP—and, as other commentators have noted, borrowed academic authority. A kind of technical terminology debt.

### 2008 — Fielding's Response

Fielding himself published the blog post "REST APIs must be hypertext-driven."

> "I am getting frustrated by the number of people calling any HTTP-based interface a REST API."

Rails became one of the dominant web development frameworks from the late 2000s through the 2010s. For the generation that "learned the Web through Rails," learning REST was virtually synonymous with learning Rails routing conventions. People read DHH's blog posts and REST tutorials, but rarely Fielding's dissertation.

---

## The Birth of "RESTish APIs"

What emerged is what we now call "RESTful APIs"—RESTish, or "REST in name only." Here are the conventions:

**URL design**: Resources expressed as nouns, with hierarchical structure in the path.

```text
GET  /users
GET  /users/123
GET  /users/123/posts
```

**HTTP method-to-CRUD mapping**:

| Method  | Operation       |
|---------|-----------------|
| GET     | Read            |
| POST    | Create          |
| PUT     | Full update     |
| PATCH   | Partial update  |
| DELETE  | Delete          |

**Status codes**: Business logic outcomes expressed through HTTP status codes—`200 OK`, `201 Created`, `404 Not Found`, `422 Unprocessable Entity`, etc.

**Data format**: JSON

**API specification sharing**: Endpoints, parameters, and response formats are described in external documentation such as OpenAPI (Swagger) and shared with developers.

This is what passes today for "RESTful API design best practices." Compare it against Fielding's four constraints of the Uniform Interface. The Uniform Interface is not about HTTP methods.

### The Spread of "RESTish APIs"

In 2007, O'Reilly published "[RESTful Web Services](https://www.oreilly.com/library/view/restful-web-services/9780596529260/)" (by Leonard Richardson and Sam Ruby). It was the first major book bearing the REST name. The book systematically presented the CRUD-over-HTTP style as "RESTful," demonstrating implementations in Ruby on Rails, Restlet (Java), and Django (Python).

Rails embedded it as convention. The book systematized it. Tech blogs and tutorials referenced both, and the developers who read those blogs wrote yet more tutorials. "RESTful API design" self-replicated without ever passing through Fielding's dissertation.

### The Richardson Maturity Model

In 2008, the same Leonard Richardson presented the "Richardson Maturity Model" at QCon—a model classifying the "maturity" of REST APIs into four levels. In 2010, Martin Fowler wrote an [article](https://martinfowler.com/articles/richardsonMaturityModel.html) about it, bringing it to wide attention.

| Level   | Description |
|---------|-------------|
| Level 0 | Using HTTP as mere transport (POX) |
| Level 1 | Assigning URIs to individual resources |
| Level 2 | Using HTTP methods (GET/POST/PUT/DELETE) properly |
| Level 3 | HATEOAS—including links in responses to guide clients |

Level 2 earns you the label "RESTful"; Level 3 gets you "full REST"—that is how this model reads.

Fielding said you cannot call it REST without HATEOAS. But in this maturity model, HATEOAS is Level 3—the highest level, positioned as an "ideal to aspire to" rather than a requirement.

Fielding's REST was a distillation of the Web's architectural design principles. The Richardson Maturity Model was a distillation of "REST in name only." And **Martin Fowler** gave it his imprimatur.

### Codification as Best Practice

In 2012, the API management company Apigee published "[Web API Design — Crafting Interfaces that Developers Love](https://web.archive.org/web/2024/https://pages.apigee.com/rs/apigee/images/api-design-ebook-2012-03.pdf)." After referencing Fielding's dissertation, it declared:

> "We advocate pragmatic, not dogmatic REST."

It labeled those who sought fidelity to Fielding's dissertation "RESTifarians" (REST fundamentalists), positioning "pragmatic REST" as the opposite. The content consisted of rules like "don't use verbs in URLs," "two base URLs per resource," and "express CRUD operations with HTTP methods"—a set of rules found nowhere in Fielding's dissertation.

Design principles for loose coupling in distributed hypermedia had been turned into a tightly coupled client-server contract, codified and systematized—and putting version numbers in URLs became received wisdom.

By this point, it bordered on comedy. They cited the dissertation as their source, dismissed its content as "dogmatic," and codified rules absent from the dissertation as "best practices." Then in 2016, Google acquired Apigee, lending corporate authority to the codification.

### A Correction That Came Too Late

In 2013, O'Reilly published "[RESTful Web APIs](https://www.oreilly.com/library/view/restful-web-apis/9781449359713/)" (by Richardson, Mike Amundsen, and Ruby), intended as a "complete replacement" for the earlier book, re-centering hypermedia.

But it was too late. The deliberate misuse had been systematized, legitimized, and solidified. The copies of copies were already unstoppable.

### The Weakness of Corrections

Corrections were attempted. Voices saying "that is not Fielding's REST" surfaced from time to time. Fielding himself responded in his 2008 blog post.

But each time, the discussion was redirected to the question of "which is more practical." "HATEOAS is ideal but not realistic." "Working code matters more than academic correctness." Thus a factual question (*what is Fielding's REST?*) was substituted with a value judgment (*which is more practical?*).

Once this substitution occurs, corrections cease to function. "That's factually wrong" versus "but it's convenient" is not a real rebuttal, yet it appears to settle the argument. As a result, the misuse persisted uncorrected.

---

## Three Distinct Questions

This history contains three separate questions:

1. **A factual question** — What did Fielding's dissertation actually say?
2. **A practical question** — Is CRUD-over-HTTP useful?
3. **A naming question** — Did it need to be called "REST"?

The answer to question 2 is "yes." Using HTTP methods and status codes directly, exchanging JSON—this approach was reasonable, especially in contrast with SOAP.

But the correctness of question 2 does not answer questions 1 or 3. In the "REST debates," these three have been compressed into a simple binary: "academic correctness vs. practicality." Under this compression, questioning facts or naming gets dismissed as an attack on practicality. Say "that is not Fielding's REST" and you get "but it's convenient." The questions are different, yet the response appears to be an answer.

Meanwhile, the industry has built a massive ecosystem on top of the misuse. "RESTful APIs" already function as something separate from Fielding's REST—carrying the burden of misuse along with them.

And so the copies of copies of "REST" continue to this day. That is where we are.

## Epilogue — When Will They Learn

In 2017, Fielding said this in his talk at ESEC/FSE 2017:

> "REST has become a buzzword. There's nothing particularly wrong with that… unless you happen to be me or working with me."

In 2021, prompted by the TwoBitHistory article on REST, Fielding posted a Twitter thread. Blaming his candor on vaccine booster side effects, he wrote:

> One problem with "mostly right" takes on my dissertation is when they see the trees but not the forest. REST isn't about APIs; it's about system interaction for network-based apps. But the constraints were also designed to induce a uniform network-based API (URI+HTTP+HTML+etc).
>
> Yes, REST is a buzzword, it has been abused (as expected), and the name refers to an architectural style in a dissertation about software engineering. However, seeing the Web through REST goggles does reveal the general-purpose network-based API designed within that architecture.
>
> So, then we have this problem of people wanting an API for their network-based application, being told they should learn from REST, and interpreting that as "use REST" like they would use a library import. That's not how it works, and recipients eventually blame the advice. Fine.
>
> It doesn't really work for me to sit here and blame practitioners for not understanding how to design their own large-scale software systems using my advice, that I provided in my dissertation, since I was the one who decided that wasn't my audience at the time. Fine.
>
> But at what point do people realize that the reason they are paid to do their jobs, or being respected for the great jobs that they already do, is because they can use their own brains to fill in the details specific to their own system context? **Learn, apply, iterate!**


---

## References

- 2000: Roy T. Fielding, [Architectural Styles and the Design of Network-based Software Architectures](https://roy.gbiv.com/pubs/dissertation/top.htm)
- 2004: Matthew J. Dovey, [C.R.U.D. WebServices and Record Update](https://www.ceridwen.com/srw/record-update/crud-html/)
- 2004: Bob DuCharme, [Telnet and REST Web Services?](https://www.xml.com/pub/a/2004/12/15/telnet-REST.html) (xml.com)
- 2007: DHH, [Good times at RailsConf Europe](https://dhh.dk/posts/12-good-times-at-railsconf-europe)
- 2007: Ruby on Rails, [Rails 2.0: It's done](https://rubyonrails.org/2007/12/7/rails-2-0-it-s-done)
- 2008: Roy Fielding, [REST APIs must be hypertext-driven](https://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)
- 2010: Martin Fowler, [Richardson Maturity Model](https://martinfowler.com/articles/richardsonMaturityModel.html)
- 2012: DHH, [The Parley Letter](https://dhh.dk/2012/the-parley-letter.html)
- 2012: DHH, [Getting hyper about hypermedia APIs](https://signalvnoise.com/posts/3373-getting-hyper-about-hypermedia-apis)
- 2012: Apigee (Brian Mulloy), [Web API Design — Crafting Interfaces that Developers Love](https://web.archive.org/web/2024/https://pages.apigee.com/rs/apigee/images/api-design-ebook-2012-03.pdf)
- 2017: Roy Fielding et al., [Reflections on the REST Architectural Style](https://dl.acm.org/doi/10.1145/3106237.3121282) ESEC/FSE ([slides](https://roy.gbiv.com/talks/201709_Fielding_REST.pdf))
- 2020: TwoBitHistory, [Roy Fielding's Misappropriated REST Dissertation](https://twobithistory.org/2020/06/28/rest.html)
- 2021: Roy Fielding, [Twitter](https://x.com/fielding/status/1458499369204785152)

<p style="color: #aaa; font-size: 0.75em; margin-top: 3em;">What this article did not say: <a href="/blog/2026/03/03/rest-how-it-became-buzzword-unwritten-en" style="color: #bbb;">the unwritten</a></p>
