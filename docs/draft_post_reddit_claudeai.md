Title:
Two Claude Code Power Moves: /question research mode + Bypass Permissions in Shift+Tab

BODY:
Hey folks,

I’ve been slowly tuning my Claude Code setup and landed on two small tweaks that made a big difference in day-to-day use. Figured I’d share in case it helps someone else (or sparks better versions).

TL;DR

Custom /question command → “pure research mode” that only reads + explains, never touches files or runs risky commands.

Bypass Permissions via Shift+Tab → no more restarting Claude Code with --dangerously-skip-permissions just to get into bypass mode.

Both are simple config changes, and they work across all sessions.

1. /question – strict Q&A / research mode

Problem:
Sometimes I just want Claude to explain things — how auth works, what a function does, where an error originates — without it immediately trying to refactor or create files. Out of the box, it’s very “task completion” oriented.

Fix:
I added a custom /question command that:

✅ Uses all the read-only research tools (Read, Grep, Glob, WebSearch, etc.)

✅ Gives structured, source-backed answers

❌ Never creates/edits files

❌ Never runs state-changing commands

❌ Never uses TodoWrite or “take action” tools

Examples I actually use:

/question How does authentication work in this codebase?
/question Where are the main API endpoints defined?
/question What are the key env vars and where are they referenced?
/question What's the difference between useState and useReducer in this project?


How I wired it up

Create:

mkdir -p ~/.claude/commands
nano ~/.claude/commands/question.md


Drop something like this in:

---
description: Enter Q&A mode - research and answer questions without taking actions
argument-hint: <your question>
allowed-tools: [Read, Grep, Glob, Bash, WebSearch, WebFetch, Task]
model: sonnet
---

You are in QUESTION MODE - a strict research-only session.

## Core directive
Answer the user's question by gathering information with read-only tools. Do not change system state.

## User's question
$ARGUMENTS

## Absolute NOs
- Do NOT create, edit, or delete files
- Do NOT run state-changing bash (git commit, npm install, etc.)
- Do NOT use TodoWrite or modify configs

Only allow read-only bash like: ls, cat, git status, git log, git diff.

## Response format
1. Direct answer
2. Evidence (files, paths, line refs)
3. Context / explanation
4. Sources consulted

Use GitHub-flavored markdown and keep everything grounded in what you actually read.

Now answer the question: **$ARGUMENTS**


After that, /question ... is available everywhere.

Why it’s nice:

Lets me “interview” the codebase safely

Forces Claude to be evidence-based instead of guessing

No surprise edits while I’m just trying to understand things

2. Bypass Permissions as a mode in Shift+Tab (no restart)

Old behavior:
If I wanted to run in “bypass permissions” mode, I had to:

Quit my current Claude Code session

Restart with something like claude --dangerously-skip-permissions

Lose context just to switch how permissions work

Annoying if you’re in a long debugging or refactor session.

New behavior:
I tweaked settings.json so “Bypass Permissions” shows up as a selectable mode when I hit Shift+Tab to cycle modes. Now it’s just:

Normal → Accept edits → Plan → Bypass Permissions

No restart, no lost context.

What I changed

Open:

nano ~/.claude/settings.json


Then set up your permissions roughly like this (adjust paths/commands to your risk tolerance):

{
  "permissions": {
    "allow": [
      "Read(/Users/YOUR_USERNAME/**)",
      "Write(/Users/YOUR_USERNAME/**)",
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(git status:*)",
      "Bash(git diff:*)"
    ],
    "deny": [
      "Bash(sudo:*)",
      "Bash(rm -rf:*)",
      "Write(/System/**)",
      "Write(/etc/**)"
    ],
    "ask": [
      "Bash(git push:*)",
      "Bash(npm install:*)",
      "Bash(pip install:*)"
    ],
    "defaultMode": "acceptEdits"
  }
}


Then:

Restart Claude Code once so it picks up the config

Use Shift+Tab in the CLI to cycle modes → you should now see the bypass option appear as one of them

Now when I’m working in a trusted repo and need Claude to move fast, I just flip into bypass mode for a bit, then flip back to something safer.

How I use both together

Typical flow for me now:

# Step 1: Understand (safe)
/question How is authentication wired up and where are tokens validated?

# Step 2: Implement (normal / accept-edits mode)
Refactor the auth middleware to support JWT rotation with minimal changes.

# Step 3: Occasionally flip into bypass mode
# When I know exactly what I want Claude to run and I'm OK with it


It keeps a nice separation between:

Research / explanation

Editing / refactoring

Full send / bypass on trusted projects

If you want to dig deeper

Official Claude Code docs: https://code.claude.com/docs/en/overview

Cheat sheet that helped me think about this like a little Unix-y REPL: https://shipyard.build/blog/claude-code-cheat-sheet/

Curious what others are doing

If anyone has:

Better patterns for permission presets

Other useful custom commands (/audit, /docs, /tests, etc.)

Safer ways to structure bypass / “dangerous” modes

…I’d love to steal/learn from your setups.
