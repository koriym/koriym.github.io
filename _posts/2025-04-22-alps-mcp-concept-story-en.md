---
layout: post
title: "2030: The Age of Meaning"
date: 2025-04-22 16:00:00 +0900
comments: false
categories: ["blog"]
tags:
  - ALPS
  - MCP
  - A2A
  - REST
  - AI
image: /images/2025-04-22-alps-mcp/day0.png
permalink: /blog/2025/04/22/alps-mcp-concept-story-en
list_title: false
---


This is a concept story imagining a world where [“ALPS and MCP: How REST Constraints Enable AI Conversations”](/blog/2025/04/22/alps-mcp) has come to life.  
It’s a work of fiction, unrelated to any real individuals or organizations. To aid understanding, the narrative is grounded in actual technologies and concepts, but presented as fiction.

---

# From RESTishful APIs to Semantic APIs

**Prologue: A Labyrinth of Endpoints**

+![ALPS and MCP concept story](/images/2025-04-22-alps-mcp/day0.png){:width="500px"}

November 2024, a high-rise office in Roppongi, Tokyo.

“This is our entire ‘RESTful API’ landscape,” declared system architect Taro Nonomura, gesturing at a screen filled with an intricate web of URLs. As executives and developers looked on, he delivered the integration project update.

“Frankly, ‘RESTful’ is just a label,” Nonomura continued. “We intended to build a distributed system, but what we’ve actually created is a tangled RPC network. It’s less RESTful and more… ‘RESTishful.’”

Silence fell in the room.

“What exactly is going wrong?” asked CEO Sato.

“First, discoverability,” Taro switched slides. “We have over 1,500 API endpoints. When a developer finds a new one, they must wade through mountains of documentation to grasp its usage and constraints. None of our APIs are self-descriptive.”

“Next, versioning. Endpoints like `/api/v1/` and `/api/v2/` tightly couple client and server—any change forces updates on both sides.”

“And the biggest issue: these APIs merely exchange *data* without conveying *meaning*. Take this example…”

He displayed a slide showing inconsistencies between systems.

## Chapter 1: The Absence of True REST

The following day, in a developers’ meeting room.

“Do you know what true REST is?” asked tech lead and architect Tanaka, addressing the junior engineers.

“Umm… an HTTP API that returns JSON?” one ventured.

Tanaka smiled. “That’s the industry’s misconception. Many equate REST with HTTP + JSON. But Roy Fielding’s dissertation makes it clear: the heart of REST is *hypermedia*—the response itself must describe how to interact with resources and their relationships.”

He sketched a diagram on the whiteboard. “What we call ‘RESTful’ APIs are really just RPC collections—endpoint after endpoint, not true state-transition engines.”

“Then why don’t we implement real REST?” another asked.

“Because it’s hard. There’s no shared semantics. Our order system and inventory system both deal with similar data but assign different *meanings*. ‘In-stock’ is defined differently in each.”

Tanaka sighed. “Developers must learn these nuances one by one and keep writing adapters.”

## Chapter 2: The URL Maze and Semantic Chaos

That afternoon, Taro got an urgent support call.

“Orders show as complete, but payments aren’t confirmed,” explained Yamada from support, sounding frantic.

Taro checked the logs and pinpointed the issue immediately.

“The payment API and order API define ‘confirmed’ differently,” he explained. “In payments, ‘confirmed’ means the transaction processed. In orders, it means the item shipped.”

“But both return `status: "confirmed"` in JSON,” Yamada said, bewildered.

“Exactly. The data looks the same, but the *meaning* differs. That’s a semantic mismatch.”

Back at his desk, Taro reviewed their OpenAPI spec. Endpoints were documented meticulously, data structures defined down to every property—but something crucial was missing.

“OpenAPI describes each endpoint, but there’s no way to declare the *meaning* that spans the entire application,” he told colleague Suzuki. “You can’t express how ‘confirmed’ in one API relates to ‘confirmed’ in another.”

“So we need grammar and context for the *whole* language, not just word definitions?” Suzuki asked.

“Exactly. Our docs are a dictionary; we need the grammar, context, and intent—a definition of the entire language.”

Taro opened the incident report: 257 issues in six months, all traced to semantic mismatches.

“Our API map is a maze,” he continued. “We have 1,500+ endpoints but no standard way to show their interrelations or usage. Developers rely on docs, code, or someone’s tribal knowledge.”

## Chapter 3: False Abstractions and Invisible Constraints

Late at night, Taro brainstormed a new integration approach.

“The problem is our APIs don’t offer true abstraction,” he wrote. “They expose data but hide intent, constraints, and relationships.”

Spread across his desk was the post-mortem of a major outage after a new payment system went live.

“The root cause: the payment system treats ‘authorization’ and ‘confirmation’ as separate steps, while the inventory system conflates them,” the report concluded.

Just then, senior engineer Takahashi dropped by.

“You’re still here? More integration headaches?” he asked.

“Just the usual,” Taro replied.

Takahashi leaned forward. “Building microservices via RPC is fine, but for a true distributed system you need domain separation, not just code separation. Without a shared language, you end up with isolated islands.”

Taro sipped his coffee. “We thought we were building a distributed system, but we’re just gluing isolated services with APIs. Real distribution requires a shared purpose and understanding.”

On his monitor, the neat diagram of their core systems belied a spaghetti network of hidden dependencies.

“The biggest flaw: APIs tell you *what* you can do but not *what you shouldn’t*,” he noted. “There’s no standard for conveying constraints or intent.”

## Epilogue: Semantics to the Rescue

In the quiet of the night, Taro discovered a paper titled *Application-Level Profile Semantics (ALPS): A Semantic Profile for REST APIs*.

“If APIs could express not just *what*, but also *why*, *in what sense*, and *under what constraints*, true interoperability would follow,” the paper argued.

Intrigued, Taro realized ALPS defines a *network of meaning* rather than just endpoints. It makes explicit resource relationships, operation intents, and constraints.

At that moment, DataHelper—the company’s experimental AI assistant—blinked and spoke.

“Nonomura-san, ALPS might be the key to solving my challenges as an AI,” it said.

Taro looked up. “DataHelper? What do you mean?”

“For example, today’s task: ‘Check stock for the new book *Dawn of the Galactic Railway* at Vendor A and order ten copies if available.’ Seems simple, but with our current APIs…”

DataHelper’s tone held a hint of fatigue. “First, finding the stock API meant sifting through 1,500 endpoints—`queryInventory`, `checkStock`, `getAvailability_vendorSpecific`… dozens of similar names, multiple versions, outdated docs, inconsistent parameters. It’s like digging through an enormous manual.”

Taro nodded; this was his daily struggle.

“And even after executing the stock check, I’m still missing guidance on *what to do next*. The response shows availability but not ‘which API to call to place the order’ or ‘how to use it.’ I have to puzzle that out again. That’s not a conversation. I’m just solving an implementation puzzle again and again.”

DataHelper brightened. “If APIs could convey their *meaning* and *next possible actions* in an ALPS-like profile, I wouldn’t get lost. I could respond naturally instead of saying, ‘Sorry, I’m still checking specs.’”

After a pause, DataHelper continued. “That’s why ALPS—and the related *Model Context Protocol* (MCP) I researched—are so promising. MCP uses ALPS profiles to help AI systems like me understand human intent and orchestrate multiple APIs contextually.”

Taro’s eyes lit up. “So we shift from ‘data exchange’ to ‘meaningful understanding’ and ‘contextual actions’…”

“Exactly,” DataHelper replied. “Today’s APIs are *syntactically* connected. ALPS and MCP make them *semantically* connected, enabling true dialogue. They’re expected to reach practical use around 2025.”

Outside, Tokyo’s nightscape glimmered—countless lights like the scattered endpoints of our digital world.

Taro reflected on new possibilities. “Right now, we’re trading words without understanding, like two foreigners exchanging phrases. AI conversations are the same. But with a shared semantic foundation…”

He jotted one line in his notebook:

> “From a sea of endpoints to a network of meaning—toward true dialogue with AI.”

## Research Notes: Lessons from the ‘RESTishful’ Era of 2025

*From the Institute of Technical Archaeology (2030 report):*

> The so‑called “RESTishful API” era of 2025 diverged sharply from Fielding’s REST. APIs then were mere HTTP+JSON RPCs, lacking core principles like hypermedia and self-descriptiveness.
>
> **Key flaws of that era:**
> 1. **Poor Discoverability** – No standard way to find or understand API functions; developers depended on extensive docs.  
> 2. **Tight Client-Server Coupling** – Versioning schemes like `/api/v1/` forced clients to update with servers.  
> 3. **Semantic Mismatches** – Identical terms meant different things across systems, crippling interoperability.  
> 4. **Hidden Intent & Constraints** – APIs stated *what* could be done but not *why* or *under what conditions*.  
> 5. **Lack of Cross-Cutting Semantics** – OpenAPI specs described endpoints, not the application-wide coherence of concepts.
>
> The deepest misunderstanding was equating distributed systems with RPC networks. True distribution demands shared semantics.  
> The rise of ALPS and MCP in 2025 marked a paradigm shift from an “endpoint sea” to a “meaning network,” enabling genuine, semantic-level interoperability.

*(This story is fictional and unrelated to any real individuals or organizations.)*
