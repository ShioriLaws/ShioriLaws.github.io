---
title: "Organizing Claude Code Settings: A Three-Layer Approach"
date: 2025-01-19
draft: false
tags: ["Claude Code", "AI", "Knowledge Management", "Prompt Design"]
---

After a few days with Claude Code, I found myself puzzling over a fundamental question: where should I put my instructions? CLAUDE.md, Skills, Rules—the options are there, but the best practices aren't obvious.

## The Memory Challenge

Someone on X described AI as "incredibly smart but with amnesia." That resonated with me.

ChatGPT reportedly has better memory retention, which aligns with my experience. Claude Code, however, starts fresh with each new conversation—no recollection of yesterday's work.

I haven't compared it directly to Codex, so this might simply reflect the difference between general-purpose AI and specialized coding assistants.

Regardless, AI memory is significantly more limited than human memory. This makes deliberate context design essential.

## The Three-Layer Framework

I discovered the "three-layer structure" concept in [an article by Ren](https://note.com/ren_ai_coach/n/n25d91547a3cb) (Japanese). Two principles stood out:

1. **Shift from "writing rules" to "designing context"**
2. **Rule files are less about constraining behavior and more about curating knowledge**

The key insight: instead of commanding the AI with "do this, avoid that," you're providing it with the background knowledge it needs to perform effectively. A subtle but meaningful distinction.

## Practical Challenges I Encountered

### Determining CLAUDE.md Placement

Yesterday, I discussed building a Learning Blog with Claude inside my Obsidian vault. However, the actual project files—Python scripts, Hugo configurations—resided in a separate `dev` folder.

The question arose: which CLAUDE.md should contain what information?

Claude's recommendation was straightforward: project-specific details belong in the project folder. However, there's a caveat: **Claude doesn't read CLAUDE.md files from other directories during a conversation.**

Given this limitation, I consolidated everything into my vault's CLAUDE.md for now.

### Understanding "On-Demand Reference"

The three-layer article mentioned extracting technology-specific guidelines into smaller Rules files that the AI would "reference as needed."

The mechanism remains unclear to me. Should the main CLAUDE.md include conditional instructions like "when asked about X, consult this file"? When I asked Claude directly, automatic referencing didn't appear to be a built-in feature.

For now, I've kept everything in CLAUDE.md. I plan to revisit this approach with future projects and incorporate best practices as I discover them.

### Distinguishing Generic Skills from Project Rules

The workflow documentation Claude generated for my CLAUDE.md turned out well—clear and readable for humans too.

I also added guidelines for documentation tone—keeping things concise yet thorough. This went into CLAUDE.md alongside everything else.

However, "write concise documentation" feels like a transferable skill rather than project-specific guidance. Perhaps it belongs in Skills instead. This is something I'll continue to explore.

## Work in Progress

I don't have definitive best practices yet.

Asking AI for configuration recommendations often yields agreeable but generic responses. The practical path forward is continued experimentation to find what suits my workflow.

My current working model:
- **CLAUDE.md** → Project overview and foundational context
- **Skills** → Reusable procedures for recurring tasks
- **Rules** → Specific constraints and guidelines (technology-specific considerations)

I'll continue refining this approach.
