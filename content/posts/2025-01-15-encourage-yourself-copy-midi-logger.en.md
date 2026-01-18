---
title: "Copying a Gospel Song: The Day That Led to MIDI Logger Improvements"
date: 2025-01-15
categories: ["music"]
tags: ["gospel", "practice-log", "midi", "workflow", "obsidian-plugin"]
learned_date: 2025-12-29
draft: false
---

> Learned: 2025-12-29 / Published: 2025-01-15

## Today's Work

About 2 hours of transcription work on Donald Lawrence's "Encourage Yourself."

{{< youtube JbEaftzaFWA >}}

## What I Learned During Environment Setup

### MIDI Keyboard Recognition Issues

Had an issue where MIDI Logger wouldn't recognize my NORD.

**Solution**: In NORD's MIDI settings, I assigned everything to USB1. Even after that, it occasionally stopped being recognized, but unplugging and replugging the MIDI cable fixed it every time. Lol.

### Optimizing Monitor Placement

At first, I tried placing a portable monitor in front of the keyboard for transcribing in ABC Notation, but the screen was too small and hard to work with.

**Current Best Setup**:
- Using the large monitor on my desk. The MIDI keyboard sits perpendicular to the desk, but for capturing notes, I can input with my right hand while looking at the desk monitor. Later, I bought a monitor arm to make it easier to view from the keyboard position.
- Split the screen in two: ABC Notation staff display on one side, ABC Notation input on the other
- Having both input and output on screen at all times prevents losing track of which measure I was working on

## Improving the Obsidian MIDI Logger Plugin

This day's work led to improving the Obsidian MIDI Logger plugin.

The original plugin captures MIDI note information from a MIDI keyboard when writing ABC Notation (just note info, not duration), but it only "captured notes converted to C Major."
I created a **Key-aware version** that outputs with accidentals appropriate to the selected key.

The original version would capture F# as F# when playing a song in G key, but from an ABC Notation perspective, it should be F without an accidental. Conversely, when playing F natural, it needs a natural accidental. By solving this with the **Key-aware version**, transcribing "Encourage Yourself" in Db became much easier.

The improved version is available on GitHub:
- [ShioriLaws/midi-logger](https://github.com/ShioriLaws/midi-logger)

Original project:
- [maybe-hello-world/midi-logger](https://github.com/maybe-hello-world/midi-logger)

Everything from improving the plugin to publishing on GitHub was a first-time experience.
Building an app with ChatGPT was also a first, and even though it was a small start, I felt a great sense of accomplishment.

## Reflections

"Encourage Yourself" reminded me once again what a difficult piece it is. The difficulty isn't in the key, chord progressions, or time signature, but in:

- The subtle nuances of vocal runs
- The skill of keeping arrangements sparse while inserting cool licks at key moments

I still need to expand my vocabulary.
Transcription work like this helps build that vocabulary.

Being able to improve and publish a plugin to solve my own problem became a good example of Learning in Public.
