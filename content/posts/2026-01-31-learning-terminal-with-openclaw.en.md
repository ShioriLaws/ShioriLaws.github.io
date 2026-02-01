---
title: "Learning Terminal Commands the Hard Way: Running OpenClaw on AWS"
date: 2026-01-31
categories: ["knowledge-management"]
tags: ["openclaw", "aws", "terminal", "linux", "tmux", "ssh"]
draft: false
---

## What is OpenClaw?

I've been experimenting with [OpenClaw](https://openclaw.ai/), a personal AI assistant tool that's been making waves in the AI agent community.

OpenClaw is an AI assistant that runs on your own machine (or in the cloud) and can be controlled through chat apps like Discord and Telegram. It can do browser automation, file operations, shell commands — pretty much anything you'd want an agent to do.

The LLM backend is flexible — you can use ChatGPT, Claude, or other models via subscription or API. What sets OpenClaw apart is apparently some enhanced memory mechanism (something about persisting memory to files? I'm not entirely clear on the details yet).

By the way, this tool has had quite a naming journey. It launched as ClawdBot, but Anthropic raised concerns about the similarity to "Claude," so it was rebranded to MoltBot (as in molting — keeping the crustacean 🦞 theme from ClawFish). A few days later, it changed again to OpenClaw.

The community use cases are fascinating: people are letting their bots handle stock trades, manage email correspondence with vendors, or even build apps overnight on command ("make me an app while I sleep"). This week, a dedicated social network for bots launched, and now the bots are talking to each other. It's wild.

## Why AWS?

OpenClaw can run on your local machine, but I'm not comfortable installing something I don't fully understand on my main PC — that feels like a recipe for disaster. Experienced users in the community seem to prefer running it on a Mac mini for local 24/7 operation, but I'm just a curious beginner who wants to test it out safely. So I went with AWS EC2 Free Tier for always-on hosting.

And that's where the unexpected learning began.

### I've Always Been Bad at Terminals

I've always wanted to get comfortable with the terminal. I've wanted to learn Linux basics. But somehow, I never actually did it.

This time, running OpenClaw on AWS meant **terminal work was unavoidable**. I was forced to learn.

But honestly? It's been fun.

## What I Did

### 1. Basic SSH Connection

First, I needed to SSH into the AWS EC2 instance.

```bash
ssh -i ~/.ssh/your-key.pem ubuntu@[your-ec2-ip]
```

This works, but the connection drops frequently. Not great when you're in the middle of something.

### 2. Keeping SSH Alive

I found that the `ServerAliveInterval` option helps maintain the connection.

```bash
ssh -o ServerAliveInterval=30 -o ServerAliveCountMax=3 -i ~/.ssh/your-key.pem ubuntu@[your-ec2-ip]
```

But even this isn't foolproof. Enter tmux.

### 3. Session Persistence with tmux

**tmux** is a tool that maintains sessions on the server side. Even if your SSH connection drops, your work stays intact — essential for remote work.

```bash
# Install tmux
sudo apt update
sudo apt -y install tmux

# Start a new session
tmux new -s moltbot

# Detach from session
Ctrl+b → d

# Reattach to session
tmux attach -t moltbot
```

If SSH disconnects, you just reconnect and run `tmux attach` — everything is exactly as you left it. Super useful.

#### Basic tmux Commands

| Action | Command/Key |
|--------|-------------|
| New session | `tmux new -s [name]` |
| Reattach | `tmux attach -t [name]` |
| Detach | `Ctrl+b` → `d` |
| Split pane (horizontal) | `Ctrl+b` → `"` |
| Split pane (vertical) | `Ctrl+b` → `%` |
| Navigate panes | `Ctrl+b` → arrow keys |

### 4. Writing a Shell Script

Manually syncing files from my local Mac to AWS was tedious, so I wrote a shell script.

```bash
# On local Mac
mkdir -p ~/bin
nano ~/bin/sync-script.sh

# First line must be the shebang
#!/bin/bash

# Add your rsync command
rsync -avz --delete ~/local/path/ ubuntu@[your-ec2-ip]:/remote/path/

# Save: Ctrl+O → Enter
# Exit: Ctrl+X

# Make it executable
chmod +x ~/bin/sync-script.sh
```

Now I can run `~/bin/sync-script.sh` instead of typing the whole rsync command every time.

#### Making It Globally Accessible

Adding the script directory to PATH lets you run it from anywhere.

```bash
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Now this works from anywhere
sync-script.sh
```

### 5. SSH Config and Aliases

Typing `ssh -i ~/.ssh/your-key.pem ubuntu@[your-ec2-ip]` every time was painful. SSH config files make this much simpler.

```bash
nano ~/.ssh/config

# Add this
Host openclaw
  HostName [your-ec2-ip]
  User ubuntu
  IdentityFile ~/.ssh/your-key.pem

# Save and exit
# Set permissions
chmod 600 ~/.ssh/config
```

Now I can just use `ssh openclaw`.

#### Auto-Attach to tmux with an Alias

I added an alias in `.zshrc` to connect via SSH and automatically attach to my tmux session.

```bash
nano ~/.zshrc

# Add at the end
alias claw='ssh -t openclaw "tmux attach -t moltbot || tmux new -s moltbot"'

# Save and exit
source ~/.zshrc
```

Now typing `claw` in the terminal connects me to AWS and drops me into my tmux session. `Ctrl+b` → `d` brings me back to my local shell.

It's incredibly convenient.

## What I Learned

Working through this clarified some concepts that were always fuzzy for me.

### The OS / Shell / Command / Editor Hierarchy

At first, I thought "tmux is an editor" and "nano is a command." But this diagram helped me understand the layers:

```
[OS]
 └─ [Shell] zsh / bash
      └─ [Command] ssh, rsync, tmux
           └─ [Editor] nano, vim
```

- **OS**: macOS, Ubuntu, etc.
- **Shell**: The interface that accepts commands (zsh, bash)
- **Command**: Tools run from the shell (ssh, tmux, rsync)
- **Editor**: Tools for editing files (nano, vim)

tmux is a command. nano is also a command. They're on different layers.

### Command History Search

You can search past commands with `Ctrl+R` in the terminal, but I found it clunky and often couldn't find what I wanted.

In the end, SSH config and aliases solved the problem more effectively, so I stopped worrying about history search.

## Next Steps

I've got the basics of SSH and tmux down. Up next:

- Read OpenClaw-generated Markdown files directly in the tmux session
- Learn `cat` and `less` for file viewing
- Actually run OpenClaw and see how it performs in practice

## Summary

I started out just wanting to try OpenClaw. Instead, I accidentally learned the fundamentals of terminal and Linux usage.

Something I've been meaning to do for years finally happened because I actually needed it. **Necessity really does accelerate learning.**

I still can't say I've mastered tmux, SSH config, aliases, or shell scripts — but I'm getting there, little by little.

As a beginner, I'm being careful and taking it one step at a time.

---

**Related posts:**
- [AI Project Management Agent, Part 2](/en/posts/2026-01-22-project-management-ai-agent-part2/)
