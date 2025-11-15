# Claude Code Power Modes

Dial in Claude Code with two complementary workflows: `/question` for zero-risk research, and a “Bypass Permissions” mode you can toggle from Shift+Tab without restarting the CLI. Both mirror the setup outlined in the [Reddit draft](docs/draft_post_reddit_claudeai.md) and [reference notes](docs/reference.md).

## Feature Overview

### `/question` — strict research & Q&A
- Keeps Claude in **QUESTION MODE**, a read-only analysis flow.
- Uses tools like `Read`, `Grep`, `Glob`, `WebSearch`, and `WebFetch` to gather evidence.
- Never creates, edits, or deletes files.
- Only permits low-risk bash commands (e.g., `ls`, `cat`, `git status`, `git diff`).

#### Example prompts
```text
/question How does authentication work in this codebase?
/question Where are the main API endpoints defined?
/question What are the key env vars and where are they referenced?
```

Use it whenever you want source-backed explanations before making changes.

### Bypass Permissions — Shift+Tab mode toggle
- Adds a **Bypass Permissions** entry to Claude Code’s mode switcher (Shift+Tab).
- No need to restart with `--dangerously-skip-permissions` just to unlock elevated actions.
- Ideal for trusted repositories where you occasionally want faster iteration.

## Safety & Best Practices
- Keep bypass mode reserved for repos you trust; drop back to safer modes for routine work.
- Maintain strict `deny` and `ask` lists so surprising commands still get blocked or require confirmation.
- Replace placeholder paths (e.g., `YOUR_USERNAME`) with your actual username before using the configs.
- Review configs regularly to match your personal security comfort level.

## Configuration Files

| File | Purpose |
| --- | --- |
| `.claude/commands/question.md` | Defines QUESTION MODE with strict read-only behavior and structured answers. |
| `.claude/settings.json` | Sets allow/deny/ask permissions and exposes the bypass mode in Shift+Tab. |

### Link References
- [Official Claude Code docs](https://code.claude.com/docs/en/overview) *(placeholder — update if a newer link exists).* 
- [Shipyard Claude Code cheat sheet](https://shipyard.build/blog/claude-code-cheat-sheet/) *(placeholder — update if mirrored elsewhere).* 

### Getting Started
1. Copy the files in this repo to your local `.claude/` directory.
2. Update usernames and paths to match your environment.
3. Restart Claude Code once so it picks up the new command and permissions.
4. Use Shift+Tab to flip between modes while keeping your session context intact.

Stay curious, experiment safely, and share improvements back with the community!
