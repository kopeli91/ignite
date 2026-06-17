# ignite

A personal, LLM-maintained **work wiki** — packaged as a single Claude skill.

`ignite` turns the start of every work session into a ritual that compounds: on first run it sets up a small wiki for you; from then on, every session begins by reading that wiki back — recent activity, learnings, and open loops — and proposing 2–3 concrete next actions. Every session deposits what was learned; every morning starts by reading it forward.

## What it does

- **First run** — asks your name, role, and where to keep the wiki, then scaffolds it (`index`, `log`, `learnings`, `open-loops`) and writes a tiny config at `~/.claude/ignite-os.json`.
- **Every session after** — loads the wiki, surfaces the last activity + the learnings to apply today + any overdue/soon open loops, and suggests what to work on next.
- **During the session** — appends a one-line entry to the log whenever durable work is produced, and captures corrections into learnings.

## Install

**Claude Code (local skill):**
```
mkdir -p ~/.claude/skills/ignite
cp ignite/SKILL.md ~/.claude/skills/ignite/SKILL.md
```
Then start a session with `/ignite`.

**claude.ai (personal or org skill):**
Zip the `ignite/` folder and upload it under **Settings → Capabilities → Skills**.

## Usage

Type **`/ignite`** (or “start my day”). The first run walks you through ~2 minutes of setup; after that it just kicks off the session.

## Notes

- **Self-contained** — one `SKILL.md`, no hooks and no external dependencies.
- **Config-driven** — everything derives from `~/.claude/ignite-os.json`; no hard-coded paths.
- Works nicely alongside a local notes app (e.g. point Obsidian at the wiki folder to browse/edit it by hand).
