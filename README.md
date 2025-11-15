# Claude Code Power Modes

This repo contains a minimal setup for two complementary Claude Code workflows:

1. **`/question`** – a strict, read-only research & Q&A mode.
2. **Bypass Permissions mode** – a more permissive mode you can toggle via **Shift+Tab** instead of restarting Claude Code with `--dangerously-skip-permissions`.

The goal is to make it easy to **understand a codebase safely**, then **move fast in trusted projects** when you’re ready.

---

## Features

### 1. `/question` — strict research & Q&A mode

`/question` keeps Claude in a **read-only, analysis-only** flow:

- Uses tools like `Read`, `Grep`, `Glob`, `WebSearch`, and `WebFetch` to gather evidence.
- **Never** creates, edits, or deletes files.
- Only permits low-risk bash commands (e.g. `ls`, `cat`, `git status`, `git diff`).
- Responds with structured answers that point back to concrete sources.

#### Example usage

```bash
/question How does authentication work in this codebase?
/question Where are the main API endpoints defined?
/question What are the key env vars and where are they referenced?
/question What changed in the last 10 commits that could explain this bug?
```

Use `/question` whenever you want **source-backed explanations** before making changes.

---

### 2. Bypass Permissions — mode you can toggle with Shift+Tab

By default, Claude Code is cautious and asks for permission frequently.  
Sometimes, in a **trusted repo**, you want it to move faster.

This config is designed so that:

- A **“Bypass Permissions”** (or equivalent) mode is exposed in the **mode switcher** (`Shift+Tab`).
- You don’t need to restart the CLI with `--dangerously-skip-permissions` just to unlock elevated actions.
- You can flip into a more permissive mode briefly, then **drop back to safer modes** for normal work.

Typical flow:

1. Start in a safe mode (e.g. accept-edits / ask-before-risky).
2. Use `/question` to understand what’s going on.
3. When you’re ready and the repo is trusted, toggle into bypass mode via **Shift+Tab** to let Claude run more of its own plan.
4. Switch back to a safer mode when you’re done.

---

## Safety & Best Practices

These configs are meant as **examples**, not one-size-fits-all security policy. A few guidelines:

- Keep bypass mode **reserved for repos you trust** (your own projects, sandboxes, etc.).
- Maintain strict `deny` and `ask` lists so surprising commands still get blocked or require confirmation.
- Replace placeholder paths (e.g. `YOUR_USERNAME`) with your actual username and directories.
- Periodically review your permissions as your workflow changes.

When in doubt, err on the side of:

- More things in `ask`
- Fewer things in `allow`
- Aggressive use of read-only tools + `/question` for exploration

---

## Files in this repo

| File                              | Purpose                                                                 |
| --------------------------------- | ----------------------------------------------------------------------- |
| `.claude/commands/question.md`    | Defines **QUESTION MODE** with strict read-only behavior and structure. |
| `.claude/settings.json`           | Example permissions config, including how bypass-style behavior is set. |

These are intentionally minimal so you can copy, tweak, and extend them.

---

## How to use this setup

### 1. Copy into your environment

Clone the repo or copy the relevant files:

```bash
git clone https://github.com/jerven-admin/claude-code-power-modes.git
cd claude-code-power-modes
```

Then either:

- Use the `.claude` folder **as-is** in this project, or  
- Copy the contents into your global Claude Code config (e.g. `~/.claude/`), adjusting paths as needed.

Example (global install):

```bash
mkdir -p ~/.claude/commands
cp .claude/commands/question.md ~/.claude/commands/
cp .claude/settings.json ~/.claude/
```

Update any usernames/paths in `settings.json` before relying on it.

---

### 2. Restart Claude Code

After you copy or modify the configs, restart your Claude Code CLI once so it picks up:

- The new `/question` command
- Any modified permissions and modes in `settings.json`

Then, inside a project directory, simply run:

```bash
claude
```

You should now be able to:

- Call `/question ...` directly.
- Use **Shift+Tab** to cycle modes, including your more-permissive/bypass mode (naming may vary based on your tweaks).

---

### 3. Example workflow

```bash
# Step 1 — Understand safely
/question How is authentication wired up and where are tokens validated?

# Step 2 — Let Claude propose changes (normal mode)
Refactor the auth middleware to support JWT rotation with minimal changes.

# Step 3 — Trusted, fast iteration (bypass mode)
# Toggle via Shift+Tab when you're comfortable with Claude running more of its own plan.
```

This keeps a clear separation between:

- **Research / explanation** (read-only)
- **Editing / refactoring** (guardrails on)
- **Highly permissive** work (bypass mode in trusted contexts)

---

## References

These links are useful if you want to adapt this setup further:

- Official Claude Code docs: https://code.claude.com/docs/en/overview  
- Shipyard Claude Code cheat sheet: https://shipyard.build/blog/claude-code-cheat-sheet/  

Always prefer the latest official documentation if anything here drifts out of date.

---

## Contributing / Customizing

Feel free to:

- Fork this repo and adjust the prompts and permissions for your own workflow.
- Add more commands (e.g. `/audit`, `/tests`, `/docs`, `/review`) that follow the same pattern:
  - Clear description
  - Specific `allowed-tools`
  - Strict “NOs”
  - Structured response format

If you come up with better patterns for:

- Permission presets
- Mode naming and descriptions
- Safer but still fast “bypass-ish” configurations

…consider sharing them back as issues or PRs so others can benefit too.
