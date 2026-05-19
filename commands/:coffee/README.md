# ☕ /coffee — Claude Code Context Reset

A custom slash command for Claude Code that performs a structured context reset. When your session gets heavy, `/coffee` saves what matters, compacts the context, and wakes Claude back up fully oriented.

## Installation

```bash
# Global (available in all projects)
mkdir -p ~/.claude/commands
cp coffee.md ~/.claude/commands/coffee.md

# Project-scoped (only in this repo)
mkdir -p .claude/commands
cp coffee.md .claude/commands/coffee.md
```

Optionally, add the generated notes file to your `.gitignore`:

```bash
echo "COFFEE_NOTES.md" >> .gitignore
```

## Usage

```
/coffee               → latte (default): write notes + compact
/coffee espresso      → quick compact only, no notes written
/coffee latte         → explicit standard reset
/coffee cold-brew     → deep reset: notes + re-read CLAUDE.md + project scan + compact
```

## Brew Levels

| Level | What it does |
|---|---|
| `espresso` | Runs `/compact` immediately. No files written. Fast. |
| `latte` _(default)_ | Collects live context (git, TODOs), writes `COFFEE_NOTES.md`, then compacts. |
| `cold-brew` | Everything in latte, plus re-reads `CLAUDE.md` and scans the project structure. Use this when returning to a project after a long break. |

## What Gets Collected

Before compacting, `/coffee latte` and `/coffee cold-brew` gather:

- Current branch and git status
- Last 5 commit messages
- Diff stat against HEAD
- All `TODO`, `FIXME`, `HACK`, `XXX`, `NOTE`, `TEMP` markers in source files

This context feeds both the `COFFEE_NOTES.md` file and the `/compact` summary, so nothing important gets lost.

## COFFEE_NOTES.md

Written to the project root during `latte` and `cold-brew`. Survives the compaction because it lives on disk. Claude reads it back immediately after compacting to re-orient itself.

Structure:

```markdown
# ☕ Coffee Notes
_Generated: <timestamp>_
_Branch: <branch>_

## What's Being Built
## Current State
## Open Issues
## Last Decision Made
## Next Action
## Files In Flight
```

## Wake-Up Output

After compaction, Claude prints a short status line:

```
☕ Coffee break over. Back to work.

Branch  : main
Status  : 3 files changed
Next    : Implement the auth middleware
```

## Permissions

The command only uses read-only Bash tools (`git status`, `git log`, `git diff`, `grep`, `find`, `cat`) plus writing `COFFEE_NOTES.md`. It never modifies source files.

## When to Use It

- Context window is getting full and responses are slowing down
- Switching tasks within the same session
- Returning to a project after a break (`cold-brew`)
- Before a long agentic run — checkpoint first, then let it rip
