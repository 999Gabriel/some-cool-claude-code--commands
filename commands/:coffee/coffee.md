---
description: Take a coffee break — compact the session, surface what matters, and wake up fresh and focused.
allowed-tools: Bash(git status:*), Bash(git log:*), Bash(git branch:*), Bash(git diff:*), Bash(grep:*), Bash(find:*), Bash(cat:*)
argument-hint: [espresso|latte|cold-brew] (optional — controls depth of the reset)
---

# ☕ /coffee — Context Reset & Reboot

You are taking a coffee break. This command runs a structured context reset so you can continue the session with a clear head, the most important information loaded, and zero cruft weighing down your context window.

Follow the steps below **in order** and **do not skip any step**.

---

## Step 1 — Read the Brew Strength

Check `$ARGUMENTS` to determine the reset depth:

| Argument | Behaviour |
|---|---|
| `espresso` or empty | Quick reset: compact only, no notes written |
| `latte` or `standard` | Full reset: write `COFFEE_NOTES.md`, then compact |
| `cold-brew` | Deep reset: write notes, re-read CLAUDE.md, re-scan project, then compact |

If no argument is given, default to **latte**.

---

## Step 2 — Pre-Compact Situation Report (skip for `espresso`)

Before compressing anything, produce a **structured situation report** by collecting live context:

**Current git state:**
- Branch: !`git branch --show-current`
- Status: !`git status --short`
- Last 5 commits: !`git log --oneline -5`
- Staged/unstaged diff summary: !`git diff --stat HEAD`

**Open work:**
Run the following to find in-code markers:
- !`grep -rn "TODO\|FIXME\|HACK\|XXX\|NOTE\|TEMP" --include="*.py" --include="*.ts" --include="*.js" --include="*.go" --include="*.rs" --include="*.java" --include="*.md" . 2>/dev/null | head -40`

Use the above output to identify:
1. What is currently being built or changed?
2. What is broken, incomplete, or explicitly flagged?
3. What decision was made most recently (from git log messages + diff)?
4. What is the immediate next step once the break is over?

---

## Step 3 — Write COFFEE_NOTES.md (skip for `espresso`)

Write (or overwrite) a file called `COFFEE_NOTES.md` in the project root.

The file must follow this exact structure:

```markdown
# ☕ Coffee Notes
_Generated: <ISO timestamp>_
_Branch: <current branch>_

## What's Being Built
<1–3 sentences. What is the feature/task/fix in progress right now?>

## Current State
<What is done? What is partially done? What has not started?>

## Open Issues
<Bullet list of TODOs, FIXMEs, or known blockers found in the codebase or from memory>

## Last Decision Made
<The most recent architectural, design, or implementation decision — from git log or conversation context>

## Next Action
<Exactly what should happen after the break — one clear sentence>

## Files In Flight
<List of files that were recently edited or are currently relevant>
```

Be **concise and precise**. This file must fit in a single glance. Do not write more than 300 words total.

---

## Step 4 — Deep Reboot (only for `cold-brew`)

For `cold-brew`, additionally:

1. Read `CLAUDE.md` (or `AGENT.md`) if it exists in the project root: !`cat CLAUDE.md 2>/dev/null || cat AGENT.md 2>/dev/null || echo "No CLAUDE.md found"`
2. List the top-level project structure: !`find . -maxdepth 2 -not -path "*/\.*" -not -path "*/node_modules/*" -not -path "*/__pycache__/*" -not -path "*/dist/*" -not -path "*/build/*" | sort`
3. Summarise what the project is and its key conventions, so the next context window starts fully oriented.

---

## Step 5 — Compact

Now call `/compact` to compress the session.

The summary passed to `/compact` should use the following as its seed (do not literally pass this as a command — this is the framing you use to guide the compaction):

> Preserve: current task, active branch, open TODOs, last decision, next step, and files in flight.
> Discard: resolved discussions, superseded approaches, exploratory tangents that led nowhere.

**Important:** The COFFEE_NOTES.md you just wrote will survive the compaction because it is a file on disk. After compaction, read it back immediately so you re-orient yourself.

---

## Step 6 — Wake Up

After compaction completes, print the following wake-up summary to the terminal:

```
☕ Coffee break over. Back to work.

Branch  : <branch>
Status  : <clean | N files changed>
Next    : <next action from COFFEE_NOTES.md>
```

Then read `COFFEE_NOTES.md` into your active context so the session continues with full orientation.

---

## Usage Examples

```
/coffee                  → latte (default): notes + compact
/coffee espresso         → quick compact only, no notes
/coffee latte            → explicit standard reset
/coffee cold-brew        → deep reset: notes + re-read CLAUDE.md + project scan + compact
```

---

## Notes

- `COFFEE_NOTES.md` should be added to `.gitignore` if you do not want it committed. Add it if it isn't there: `echo "COFFEE_NOTES.md" >> .gitignore`
- This command is safe to run at any time — it never modifies source files, only reads and writes `COFFEE_NOTES.md`.
- Running `/coffee` when context is nearly full is the recommended use case. Think of it as a checkpoint.
