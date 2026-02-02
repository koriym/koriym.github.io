---
layout: post
title: "Irreversible Change—The Era of AI-Written Code (2026)"
date: 2026-02-02 10:00:00 +0900
comments: false
categories: ["blog"]
tags:
  - AI
image: /images/2026-02-02-irreversible-change-2026/cover.png
permalink: /blog/2026/02/02/irreversible-change-2026-en
list_title: true
---

+![4E](/images/2026-02-02-irreversible-change-2026/cover.png){:width="500px"}

Exactly one year ago, I wrote an article with the same title: "[Irreversible Change—The Era of AI-Written Code](/blog/2025/01/16/Irreversible-change/)."

In it, I wrote something almost prophetic about the "declining value of Developer Experience (DX)" in the coming age of AI-written code and the "shift in responsibility" that humans would need to bear.

One year has passed. The future arrived with a speed and a kind of violence that far exceeded my imagination.

## 2025: The Fault Line

Looking back, the change did not arrive "gradually." It was a distinct fault line.

Think back to December 2024. At a conference, I asked the audience: "How many of you know Cline?" "How many of you know DeepSeek?" Not a single hand went up.

But then January 2025 arrived. The DeepSeek shock swept across the world, and even President Trump spoke its name. Before long, Cline gained traction, and AI coding agents like Cursor and Claude Code began sweeping through the industry.

In 2024, Sonnet 3.5 demonstrated the possibility that "AI can write code." The following year, Opus 4.5 with Claude Code proved the reliability that "AI can work autonomously as an agent." It was not merely the arrival of a new tool. As McLuhan said, "the medium is the message"—the very emergence of AI agents as a medium carried the message: "the era of humans writing code is ending."

Even DHH (David Heinemeier Hansson), once a skeptic, changed his stance.[^1] Such was the shock and effectiveness. I believe it was the tolling of a bell, announcing that we in web development had crossed a point of no return.

[^1]: [https://www.linkedin.com/posts/david-heinemeier-hansson-374b18221_at-the-end-of-last-year-ai-agents-really-activity-7414594514411114496-uYCo/](https://www.linkedin.com/posts/david-heinemeier-hansson-374b18221_at-the-end-of-last-year-ai-agents-really-activity-7414594514411114496-uYCo/)

## Infinite Doing, Absent Being

By now, the act of humans writing code line by line has become something beyond mere utility—a kind of ritual. Like monks copying scriptures by hand in an age when the printing press already exists: "digital monks" in their practice. The "infinite supply of code" by AI—that has become the new normal.

AI is a virtuoso of "Doing"—generating behavior and processing. Left to its own devices, it will produce methods endlessly and pile up commits without pause. So how are we supposed to trust this infinitely generated code?

Traditionally, testing had meaning because humans understood the code. The person who wrote the code grasped its logic, knew the boundary conditions, and embedded intent in the tests. It was this premise that made tests function as a "proof of trust." But can we place the same trust in AI-written tests for AI-written code?

I conducted several experiments. One was [ext-json-schema](https://github.com/koriym/ext-json-schema), a JSON Schema validation tool. This tool was created by AI without my design, without my understanding. Yet it passes thousands of validation tests against the strict JSON Schema specification, and it also passes memory leak detection tools. Consider this: if we can detect all memory leaks and prove the code is free of contradictions, can we ship code we don't understand? Or to ask the inverse—can a junior-level developer provide a more accurate review than these tools? In an era where AI generates high-quality code infinitely, how long can humans remain effective reviewers?

There is also a tautology trap lurking here. Just as AI can generate code infinitely, it can also generate tests infinitely to raise coverage. But what if the AI misunderstands the intent of the code and writes tests based on that misunderstanding? That would mean "proving a mistake correct, on the basis of the same mistake." Code and tests sharing the same misunderstanding in a closed loop—that is the problem of tautology. If the day comes when "AI approves AI's code," we will need to express intent and outcome in a verifiable form.

Code, tests, reviews—they are all a chain of Doing (behavior). But what remains absent is Being—the definition of "what something should be."

## From DX to AX

In last year's article, I wrote that the relative value of DX (Developer Experience) would decline. In an era when humans no longer write code, what meaning is there in pursuing "ease of writing" for humans?

What becomes important instead is AX (Agent Experience)—how verifiable the environment is for AI agents. What breaks the tautological loop is a mechanically verifiable constraint that exists outside the AI. We need a structure that allows AI to autonomously verify the consistency between Intent and Outcome.

Natural language in specifications can serve as a starting point, but it cannot fully specify all behavior (DOING). Structured specifications like OpenAPI, GraphQL, and JSON Schema are a step in that direction. But is it enough to verify only inputs and outputs? If we also need mechanisms for AI to autonomously verify the process and intent of code execution, then human work will shift from "writing code" to "designing and describing constraints that AI can verify."

One year ago, I closed my article with this thought: "Perhaps our work can transcend mere code creation and become the essential work of designing the future of software." The future I said "might not come this year" arrived almost immediately, like a tsunami. And after struggling for a while, once the wave recedes, I suspect we will feel even more deeply the loneliness of "the end of the era when we typed everything by hand."
