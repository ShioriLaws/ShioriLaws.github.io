---
title: "A Week Building a Project Management AI Agent: Learning the Value of JSON Schema from Failures"
date: 2025-01-17
categories: ["knowledge-management"]
tags: ["ai-agent", "chatgpt", "json-schema", "project-management", "vibe-coding"]
learned_date: 2025-01-15
draft: false
---

> Learned: 2025-01-15 / Published: 2025-01-17

## Introduction

I spent time right after the new year building an AI agent at work.

"Spent time" might sound wasteful, but I'm convinced this will save significant time and energy later.

Starting from day one of the new year, I began building an AI agent to manage the progress of projects I'm involved in. The goal was to capture all my work in one place, consult with the agent to decide next actions, draft emails, and more.

## Phase 1: Natural Language Dialogue → MD File Management

When I first started consulting about my project progress in natural language, it worked extremely well.

To maintain continuity, I created an agent that would record our conversations in a Markdown file and keep it updated.

Since our company's AI agent doesn't yet have permission for folder operations, I adopted this workflow:

1. Upload an MD file roughly listing project names, current work, and areas of responsibility
2. Chat naturally about "what happened today"
3. The agent automatically captures Tasks and Progress and updates the MD

### Moments of Amazement

This approach worked wonderfully:

- "So-and-so raised this concern in the meeting" → "Shall I draft an escalation email? Should I add a Risk?"
- "I'm going to have a meeting with so-and-so" → "Shall I write the meeting invite?"

This was amazing...

## Problem 1: Things Break Down as MD Files Grow

However, problems emerged as the MD file grew larger:

- **Updates were made in unexpected locations**
- **When asked to output the full file, it wouldn't comply**

Since Git isn't available on my work PC yet (it was in the Company Portal but my department wasn't included—I want to get permission and submit a request somehow), I was painstakingly copying and pasting while comparing diffs in VSCode.

The bigger problem was that **since the AI was doing things heuristically, there was no consistency in how the MD file was updated**.

## Phase 2: JSON Diffs + Python Updates

I figured that relying on heuristics was causing inconsistent work, so I needed clearer rules.

I asked ChatGPT, "What if I get just the diffs for project progress and update with Python?" and it said, "That's a good idea." At that point, I didn't really understand how to receive diffs—I was just vaguely asking. From the AI's response, I gradually understood that I should receive them in JSON format.

### The Mechanism

- Have AI output diffs in JSON format
- Python script updates the relevant sections
- Insert machine-readable anchors `<!-- Project:XXX -->` for placing diffs (these become invisible in Obsidian's Read mode as comments)

That day it seemed to work okay.

## Problem 2: Limits of Heuristics

However, the JSON file the agent produced the next day didn't work.

### Failure 1: Variable Names Changed Each Time

Variable names were subtly different:
- One day: `Content`
- Next day: `TEXT`
And so on.

### Failure 2: Different Schema Assumptions Each Time

The file actually needed multiple schemas, but the AI looked at only part of it, heuristically imagined a schema, and **came up with a different schema each time**.

For example:
- Domain/Area of responsibilities
- Knowledge section for the Domain
- Tasks, Progress, Risk/Issues nested within Projects
- Overall Update Log

All of these were contained in a single MD file, but ChatGPT interpreted everything as the Project schema, forcing the Project schema onto other sections. The Python code derived from that also became chaotic—no matter what I fixed, fixing one place would break another, creating a chaotic situation.

### Failure 3: Anchor Position Errors

The agent inserted content right before the next section's anchor `<!-- Project:XXX -->`, but **there was actually human-readable heading and content before that, and the insertion happened after those**.

Example:
```markdown
## Project1
Project1 is about blah blah
<!-- Project1 -->
### Tasks
<!-- Project1:TaskList -->
- Task 1
- Task 2

## Project2
Project2 is about blah blah
[New tasks were inserted here by mistake]
<!-- Project2 -->
### Tasks
<!-- Project2:TaskList -->
```

I wanted it inserted right after Project1's Task2, but it ended up inside the next section.

### Realization

Relying on heuristics just won't work. I need to define things properly and pass them.

I had ChatGPT recreate the MD file format, the diff JSON Schema, and the Python script, but since it was inferring from ambiguous data, **it never worked no matter how many times I tried**.

## Turning Point: Asking the Right Question

Stuck, I asked ChatGPT:

> I've been having the company's Co Pilot Agent heuristically update my MD file with project content that's somewhat structured but written in free form. As content grew, confusion arose and it became difficult. So I started adding anchors like `<!-- proj:xxx>` to make it machine-readable, trying to insert new tasks at the end of the task section for that project when identified in the Agent conversation. I tried getting just the diff in JSON and using Python for Insert and Change operations, but no matter how many times I try, it doesn't work—maybe the overall structure isn't well-defined. So instead of inserting/editing the MD file, I'm thinking of holding all items in JSON and displaying only the tasks for `<!-- proj:xxx>` in date order—like a database approach. Any good ideas? Due to constraints with the company Agent, I want to pass all previous data each time, discuss progress since last time, get suggestions for new action tasks, and add them to the database.

ChatGPT replied:

> That's a good pivot. **"Keep updating the MD"** will inevitably break midway because it becomes "interpreting text meaning + estimating insertion position."
If you have company Agent constraints, the most stable approach is to think of it as **"MD for humans, JSON (source of truth) for machines."**

**It will inevitably break midway!! Why didn't you suggest this from the start?!**

But this was the lesson. **You really need to ask questions that can elicit good answers.**

## Phase 3: Robust JSON Schema + Single Source of Truth

Now I'm working with ChatGPT to create a robust JSON Schema.

### New Architecture

1. **Define a solid JSON Schema**
2. **Pass previous project files in CSV format matching that schema**
3. **Convert to JSON and start operating it as Single Source of Truth**
4. **Receive updates and changes in JSON format**
5. **Write Python to combine with original JSON and output as viewable MD file**

### Additional Ideas

It would be nice to capture full names and build a directory of people. Like "talked with this person on this date about this topic."

There are too many people in my current project, and I can't remember names at all...

## Summary: What I Learned from the Long Way Around

I took quite a detour, but considering I didn't even know what JSON really was until a couple of days ago, I'll count it as learning something new every day.

### What I Learned

1. **Heuristics have limits**
   - Asking AI to "just figure it out" works great at first but gradually breaks down (I had the same experience last year asking Gemini CLI to update about 1000 files)
   - When consistency is needed, clear rules are necessary

2. **Asking the right question matters**
   - AI focuses its answer on what's asked
   - I want to develop my questioning skills. Maybe I should ask in more open-ended ways?

3. **The value of structured data**
   - Human-readable Markdown is easy for humans to read but not suited for machine operations
   - Proper definition with JSON Schema enables consistent operations

4. **The importance of Single Source of Truth**
   - Keep the truth in one place
   - Generate viewing formats from there
   - Also, numbering each item should be done by Python rather than AI, since AI would do it inconsistently

I'm going to nail down the JSON Schema and complete the project management agent.

Still halfway there, but this process itself has been good learning.

---

**Related Posts:**
- [Building a Learning in Public Blog with Claude Code](/en/posts/2025-01-15-building-blog-with-claude/)
