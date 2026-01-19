---
title: "How I Built This Blog with Claude Code"
date: 2025-01-15
categories: ["vibe-coding"]
tags: ["claude", "agentic-ai", "hugo", "python", "obsidian"]
learned_date: 2025-01-15
draft: false
---

> Learned: 2025-01-15 / Published: 2025-01-15

## The Goal

This year I committed to learning in public. I wanted a simple pipeline: take the messy learning notes in my Obsidian vault, clean them up, and publish them as blog posts.

The dream workflow:
1. Jot down notes in Obsidian throughout the day
2. Polish them with AI help
3. Push to GitHub Pages

## Building It with Claude Code

I paired with Claude Code (the VS Code extension) to build the whole system through conversation.

### What We Made

**1. A Python CLI (`lip`)**
- `lip scan` - finds publishable notes in my vault
- `lip review` - flags potential privacy issues (phone numbers, addresses, etc.)
- `lip convert` - transforms Obsidian markdown into Hugo-compatible format

**2. Hugo Blog Setup**
- Local Hugo server for previews
- PaperMod theme
- Japanese language support baked in
- GitHub Actions for automatic deployment

**3. CLAUDE.md**
- A file containing my editing preferences and project context
- Claude reads this at the start of each session for consistency
- Over time, I'm hoping this becomes a kind of "style guide" that captures my voice

### Why Hybrid, Not Fully Automated

I originally planned to automate everything, but landed on a hybrid approach instead.

- **Automated**: scanning, privacy checks, format conversion, deployment
- **Human-in-the-loop**: the actual editing (I talk through changes with Claude)

This keeps API costs at zero, and honestly, the back-and-forth helps me think through what I actually want to say.

### A Few Design Choices

**Where to Store Posts**

I started by putting posts inside the Hugo project folder. Bad idea—I couldn't link to them from my other Obsidian notes. Now they live in a `Public/` folder inside my vault:

```
/Users/shiori/Desktop/KM/
├── Public/              ← blog posts live here
│   └── posts/
├── 05_Inbox/            ← raw learning notes
└── ...
```

**Privacy Detection Tweaks**

The initial regex was flagging TikTok URLs and timestamps as "phone numbers." Had to tune the patterns to ignore URLs.

I'd also love to eventually catch "overly personal" content—not just phone numbers, but stories that are too raw to publish. That's a longer-term project.

## Looking Back

This is my second time building something with an AI agent. The first was an Obsidian plugin that scans my daily notes for recurring frustrations and generates project plans to fix them. (Haven't published that one yet, but I should write it up too. Though "write it up" undersells it—Claude basically did the whole thing.)

Even this post started as me asking Claude to summarize what we built today, then I added my own thoughts.

I've been meaning to start a blog for years and never got around to it. Now things are moving so fast I can barely keep up. Great time to be lazy.
