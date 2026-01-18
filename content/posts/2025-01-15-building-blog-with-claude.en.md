---
title: "Building a Learning in Public Blog with Claude Code"
date: 2025-01-15
categories: ["vibe-coding"]
tags: ["claude", "agentic-ai", "hugo", "python", "obsidian"]
learned_date: 2025-01-15
draft: false
---

> Learned: 2025-01-15 / Published: 2025-01-15

## What I Wanted to Achieve

With the theme of "learning in public this year," I wanted to organize and refine learning notes from my Obsidian vault and publish them as a blog.

The ideal workflow:
1. Record daily learning in Obsidian
2. Refine with AI to make it more readable
3. Publish on GitHub Pages

## Collaborating with Claude Code

I built the system through dialogue using the Claude Code extension in VS Code.

### What I Built

**1. Python CLI Tool (`lip`)**
- `lip scan` - Scan the vault for notes ready to publish
- `lip review` - Privacy check (detecting phone numbers, addresses, passwords, etc.)
- `lip convert` - Convert from Obsidian format to Hugo format

**2. Hugo Blog**
- This runs a local Hugo server that dynamically converts Obsidian MD files to HTML
- PaperMod theme
- Japanese language support
- GitHub Actions deployment setup

**3. CLAUDE.md**
- Contains editing guidelines and project information
- Claude Code reads this each time for consistent responses
- I want to gradually enrich CLAUDE.md to reflect my personal style

### Hybrid Approach

Initially I thought about "full automation," but settled on a "hybrid approach" where the editing part is done through dialogue with AI.

- **Automated**: Scanning, privacy check, conversion, publishing
- **Dialogue**: Note editing (conversing with Claude Code)

This way there's no API cost, and dialogue allows me to convey subtle nuances better.

### Design Decisions

**Blog Post Location**

Initially I placed articles inside the Hugo project, but changed to a `Public/` folder within the Obsidian vault.

```
/Users/shiori/Desktop/KM/
├── Public/              ← Blog posts
│   └── posts/
├── 05_Inbox/            ← Learning notes
└── ...
```

This lets me edit articles within Obsidian and link to them from other notes.

**Privacy Detection Tuning**

Initially, TikTok URLs and timestamps were being falsely detected as "phone numbers."
I adjusted the patterns to exclude URLs.
Originally, "privacy awareness" meant not just phone numbers and passwords, but also excluding overly private or candid stories—but I'll make that a long-term goal.

## Reflections

This is my second time building a system through collaboration with Agentic AI. The first time was an Obsidian plugin that searches through the last 30 days of daily notes for things that look like problems, sends them to an AI via API, and creates a project plan to solve those problems. I haven't published that yet, but I want to document that process too. Though "process" might be misleading—Claude did everything almost instantly...

This article itself was drafted by asking Claude to "write up today's collaboration," which I then edited and added to.
I'd been trying to write a blog for years but couldn't—now everything is moving at an unbelievably fast pace. What a wonderful time to be a lazy person like me...
