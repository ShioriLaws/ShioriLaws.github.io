---
title: "Transcribing Gospel Music (And Accidentally Improving an Obsidian Plugin)"
date: 2025-01-15
categories: ["music"]
tags: ["gospel", "practice-log", "midi", "workflow", "obsidian-plugin"]
learned_date: 2025-12-29
draft: false
---

> Learned: 2025-12-29 / Published: 2025-01-15

## The Session

Spent about 2 hours transcribing Donald Lawrence's "Encourage Yourself."

{{< youtube JbEaftzaFWA >}}

## Setup Headaches (and Fixes)

### Getting My NORD Recognized

The MIDI Logger plugin wasn't picking up my NORD keyboard.

**Fix**: Went into NORD's MIDI settings and routed everything to USB1. Still drops out occasionally, but unplugging and replugging the cable brings it back. Not elegant, but it works.

### Finding the Right Monitor Setup

I tried putting a portable monitor right in front of the keyboard for transcription, but it was too cramped.

**What actually works**:
- I use my main desk monitor instead. My MIDI keyboard sits perpendicular to the desk, so I can play with my right hand while watching the screen. (Later added a monitor arm so I can swivel it toward the keyboard when needed.)
- Screen split in half: ABC Notation staff on one side, text input on the other
- Keeping both views up means I never lose my place mid-measure

## Down the Rabbit Hole: Fixing the MIDI Logger Plugin

This transcription session led me to fork and improve the Obsidian MIDI Logger plugin.

The original plugin captures MIDI note names as you play (no rhythm, just pitches)—but it always outputs in C major. That's a problem: if I'm transcribing a song in G major and I play F#, the plugin writes "F#" when ABC Notation expects just "F" (since F# is in the key signature). And if I play F natural, I need an explicit natural sign.

So I built a **key-aware version** that handles accidentals correctly based on whatever key you select.

Made transcribing "Encourage Yourself" in Db way less painful.

Links:
- My fork: [ShioriLaws/midi-logger](https://github.com/ShioriLaws/midi-logger)
- Original: [maybe-hello-world/midi-logger](https://github.com/maybe-hello-world/midi-logger)

This was my first time modifying a plugin, my first time publishing to GitHub, and my first time building something with ChatGPT's help. Small project, but it felt like a real milestone.

## Thoughts on the Song

"Encourage Yourself" is *hard*. Not because of the key or the changes—those are manageable. What makes it tough:

- The vocal runs have all these micro-nuances that are easy to miss
- The arrangement is sparse, but when it hits, it *hits*—knowing when to lay back and when to drop a sick lick is its own skill

I need more vocabulary. Transcription is how I'm building it.

And hey—scratching my own itch, improving a tool, and shipping it publicly? That's learning in public in action.
