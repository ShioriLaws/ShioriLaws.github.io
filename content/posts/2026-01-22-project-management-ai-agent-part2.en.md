---
title: "AI Project Management Agent, Part 2: The Python Package Nightmare and What 'Heuristic' Actually Means"
date: 2026-01-22
categories: ["knowledge-management"]
tags: ["ai-agent", "chatgpt", "json-schema", "project-management", "vibe-coding"]
draft: false
---

## First, a Correction from Last Time

In [my previous post](/en/posts/2025-01-17-project-management-ai-agent/), I used the word "heuristic" quite liberally. Turns out I was using it wrong.

I noticed this while working on the next phase of the project. The AI referred to a summarization script as "heuristic," and something felt off — that wasn't how I'd been using the term.

### What "Heuristic" Actually Means

**Deterministic heuristics** are rule-based methods derived from experience. You define a set of rules, and the same input always produces the same output. Think: "if the file is over 1MB, summarize it." A human-defined rule, applied consistently.

What LLMs do is fundamentally different:
- Approximate reasoning
- Probabilistic generation
- Producing contextually appropriate outputs based on learned patterns

People in the field sometimes loosely call this "heuristic," and I fell into the same trap. In my previous post, every time I said "the AI was doing things heuristically," what I really meant was "the AI was doing things without structure or explicit rules."

Not a huge deal — the point still came across — but I wanted to set the record straight.

---

## The Main Story: What Happened Next

In my last post, I declared that I would "nail down a proper JSON Schema and finish the project management agent." That was five days ago. Here's the progress report.

### What I Built

Following the architecture I outlined last time, I worked with ChatGPT to implement each piece:

1. **Defined a Project Schema** — A proper type definition matching what I'd been writing freeform in Markdown
2. **Created a CSV migration template** — To move existing data from Markdown into structured format
3. **Manually filled the CSV, then converted to JSON via Python** — At this point, each project became its own JSON file
4. **Built a heuristic summarization script** — A Python script that condenses all project JSONs into a single summary file (this is actually the correct use of "heuristic" — rule-based summarization)
5. **The summary file becomes the conversation starter** — I feed this to the AI agent to kick off each session
6. **Created a Patch Schema** — The agent outputs updates in a structured patch format

So far, so good.

### The Package Nightmare

Then things fell apart.

I asked for a writeback script — something to apply the received patches back to the JSON files. What I got back was **a Python package split across six files**.

I had no idea what to do with it.

My company's AI agent can't perform file operations, so I've been manually copying generated code into files. A single script? Fine. But a package means:

- Where do these folders go?
- What is `__init__.py`?
- Why won't the imports resolve?
- Where do I even run this from?

I was completely lost.

On top of that, the AI made its usual mistakes — wrong variable names, mismatches between the schema and the script logic — and every fix broke something else. Same pattern as last time. I was ready to give up.

### One More Try

But I decided to push just a little further.

What I changed this time was **sharing a folder structure snapshot with every message**. "Here's my current state." "Where should this file go?" "How do I run this?" Step by step, I walked through it with the AI.

It worked. Once the AI could see my actual file structure, it gave much more accurate guidance.

### It Ran

The writeback script now works. It supports four operations:

- Add Project (create a new project)
- Update Project (modify project metadata)
- Add Objects (add tasks, risks, etc.)
- Update Objects (modify existing tasks or risks)

I haven't tested every edge case yet, but the basic patch-apply flow is functional.

I also got a script that converts the JSON back into human-readable Markdown for review. It's still missing some fields, so that's ongoing work.

## Lessons Learned

1. **Use terminology accurately** — When you keep using a word incorrectly, it eventually causes real confusion. In my case, I got tripped up by the AI's output because my mental model of "heuristic" didn't match.

2. **Give AI proper context** — Just pasting a folder structure snapshot made a dramatic difference in response quality. "You should be able to figure it out" doesn't work.

3. **Don't give up, but change your approach** — If you keep hitting the same wall, try changing how you ask or what context you provide, rather than repeating the same request.

4. **Not understanding something isn't a blocker** — I had no idea what `__init__.py` or import paths meant. But by sharing my folder state and asking "what does this mean?" over and over, I gradually understood how Python packages work. The confusion itself was just a phase.

## Next Steps

- Test remaining operations
- Add missing fields to the Markdown export script
- Run the system in production and identify schema improvements

It's been five days since my last "still a work in progress" update, and yes, it's still a work in progress. But the progress is real. At the very least, I now know what JSON is.

---

**Related posts:**
- [Part 1: A Week of Building a Project Management AI Agent](/en/posts/2025-01-17-project-management-ai-agent/)
