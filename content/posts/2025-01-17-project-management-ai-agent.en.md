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

Right after the New Year holiday, I found time to build an AI agent at work.

Starting on the very first workday of the year, I began building an AI agent to help manage the progress of the projects I’m involved in. The goal was to capture my work in one place, consult the agent when deciding next actions, draft emails, and generally use it as a thinking partner for day-to-day work.

## Phase 1: Natural Language Dialogue → MD File Management

At the beginning, consulting the agent in natural language worked extremely well.

To keep continuity between conversations, I created an agent that would record our discussions in a Markdown (MD) file and keep it updated over time.

Because our company AI agent doesn’t yet have permission to perform folder operations, I adopted the following workflow:

1. Upload an MD file roughly listing project names, current work, and areas of responsibility
2. Chat naturally about "what happened today"
3. Let the agent extract Tasks and Progress and update the MD file

### Moments of Amazement

This approach worked wonderfully:

- “So-and-so raised this concern in the meeting”
  → “Should I draft an escalation email? Should I add this as a Risk?”

- “I’m going to have a meeting with so-and-so”
  → “Shall I write the meeting invite?”

It genuinely felt amazing.

## Problem 1: Things Break Down as MD Files Grow

As the MD file became larger, problems started to appear.

- **Updates were made in unexpected locations**
- **When asked to output the full file, the agent sometimes failed to do so**

Since Git isn’t available on my work PC yet (it exists in Company Portal, but my department isn’t included—I’d like to request access somehow), I ended up manually copying and pasting while comparing diffs in VS Code.

The bigger issue, though, was consistency.

Because the agent was updating the file heuristically, there were no clear rules governing *how* updates should be applied. As the content grew, this became increasingly unstable.

## Phase 2: JSON Diffs + Python Updates

I suspected the inconsistency was caused by relying too much on heuristic behavior, so I started looking for clearer rules.

I asked ChatGPT:

“What if I get just the diffs for project progress and update the file using Python?”

ChatGPT replied that it was a good idea.

At the time, I didn’t even fully understand how I would receive diffs—I was asking somewhat vaguely. Through the response, I gradually realized that receiving diffs in JSON format would make sense.

### The Mechanism

The idea was:

- Have the AI output diffs in JSON format
- Use a Python script to update only the relevant sections
- Insert machine-readable anchors such as <!-- Project:XXX --> to define insertion points
  (These are invisible in Obsidian’s Read mode because they’re comments.)

That day, it seemed to work reasonably well.

## Problem 2: Limits of Heuristics

The next day, the JSON produced by the agent didn’t work at all.

### Failure 1: Variable Names Changed Each Time

The variable names were subtly different each day:
- One day: `Content`
- Next day: `TEXT`
And so on.

### Failure 2: Different Schema Assumptions Each Time

In reality, the MD file contained multiple conceptual schemas, but the agent would only look at part of the file and heuristically infer a single schema each time.

For example, the MD file contained:

- Domains / areas of responsibility
- Knowledge sections for each domain
- Projects, with nested Tasks, Progress, and Risks / Issues
- An overall update log

All of these lived in one file, but the agent interpreted *everything* as a “Project” schema. As a result, it tried to force that structure onto sections where it didn’t belong.

The Python code derived from these outputs became chaotic. Fixing one place would break another, and the system quickly turned into a mess.

### Failure 3: Anchor Position Errors

Another problem was incorrect insertion positions.

The agent would insert content just before the next section’s anchor, even though there was human-readable content before that anchor.

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

New tasks intended for Project 1 were sometimes inserted into the Project 2 section instead.

### Realization

At this point, it became clear that relying on heuristics simply wouldn’t work.

I tried having ChatGPT recreate the MD file format, the diff JSON schema, and the Python script multiple times. But because everything was still inferred from ambiguous data, it never worked reliably.

## Turning Point: Asking the Right Question

Eventually, I asked ChatGPT a more fundamental question about the overall approach.

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

It would also be useful to capture people information—full names, interaction dates, and discussion topics.

There are simply too many people involved in my current projects, and I can’t remember everyone’s name.

## Summary: What I Learned from the Long Way Around

I took quite a detour. Considering that I didn’t really understand what JSON was until a few days ago, I’ll count this as learning something new every day.

### What I Learned

1. **Heuristics have limits**
   - Asking AI to “just figure it out” works well at first, but gradually breaks down (I had the same experience last year asking Gemini CLI to update about 1000 files)
   - When consistency matters, clear rules are required

2. **Asking the right question matters**
   - AI answers exactly what you ask
   - I want to develop my questioning skills. Maybe I should ask in more open-ended ways?

3. **The value of structured data**
   - Markdown is great for humans, but not for machines
   - Proper definition with JSON Schema enables consistent operations

4. **The importance of Single Source of Truth**
   - Keep the truth in one place
   - Generate views from that source
   - Item numbering should be done by Python, not AI, to avoid inconsistency

I’m now focused on locking down the JSON Schema and finishing the project management agent.

I’m still only halfway there—but the process itself has already been a valuable learning experience.

---

**Related Posts:**
- [Building a Learning in Public Blog with Claude Code](/en/posts/2025-01-15-building-blog-with-claude/)
