---
name: ignite
description: Start a work session. On first run, set up a personal LLM-maintained work wiki; thereafter each session load that wiki, surface recent activity, learnings, and open loops, and propose 2–3 concrete next actions. Use when the user types /ignite or asks to start their day / start a session.
---

# /ignite — Session kickoff (self-contained, with first-run setup)

A personal "operating system" for work sessions: a small, LLM-maintained wiki that compounds — every session deposits what was learned, every morning starts by reading it back.

## Step 0 — Config check (always first)

Read `~/.claude/ignite-os.json`.
- **Missing** → this is a first run. Go to **SETUP**.
- **Present** → go to **KICKOFF**, using `wiki_path` from the config.

---

## SETUP (first run only, ~2 minutes)

Tone: warm and brisk. Batch questions into one `AskUserQuestion` call.

1. **Ask (one batched call):**
   - **Name** — what should the wiki call you?
   - **Role** — `marketing` / `sales` / `growth or data` / `generic`.
   - **Wiki location** — default `~/work/wiki` (accept any override). If the path already has a `CLAUDE.md`, adopt it as-is and skip scaffolding.

2. **Scaffold the wiki** at `<wiki_path>` (create dirs `sources/`, `wiki/`, `wiki/projects/`, `wiki/notes/`), writing these files:

   - `<wiki_path>/CLAUDE.md`:
     ```markdown
     # Work Wiki — CLAUDE.md
     This is an LLM-maintained work wiki for {{NAME}} ({{ROLE}}). The assistant writes and maintains the pages; {{NAME}} reads them.

     ## Folders
     - wiki/index.md — master catalog of pages
     - wiki/log.md — append-only activity log (newest at bottom); add an entry whenever a session produces durable work
     - wiki/learnings.md — corrections & "next time do X" notes (newest at top)
     - wiki/open-loops.md — forward register: things owed, due, or waiting-on
     - wiki/projects/ — one page per ongoing project
     - wiki/notes/ — handoffs, partner notes
     - sources/ — raw inputs (never edited)

     ## Cadence
     Update wiki/log.md at least once per session; a fuller update for long sessions. Start sessions with /ignite; capture learnings as you go.
     ```
   - `<wiki_path>/wiki/index.md`: `# Wiki — Index\n\nMaster catalog of pages. Updated as pages are added.\n`
   - `<wiki_path>/wiki/log.md`: `# Activity Log\n\nAppend an entry (newest at bottom) whenever a session produces durable work. Format: \`## [YYYY-MM-DD] <type> | <one-line summary>\`\n`
   - `<wiki_path>/wiki/learnings.md`: `# Learnings\n\nCorrections, surprises, and "next time do X" notes. Newest at top.\n`
   - `<wiki_path>/wiki/open-loops.md`: `# Open Loops\n\n| Item | Owner | Due | Status |\n|---|---|---|---|\n`

   Substitute `{{NAME}}` / `{{ROLE}}` as you write.

3. **Write `~/.claude/ignite-os.json`:**
   ```json
   { "version": 1, "user": { "name": "...", "role": "..." }, "wiki_path": "<absolute path>" }
   ```

4. **Offer** to append a pointer to `~/CLAUDE.md` (create if absent; never overwrite) so every session knows the wiki exists:
   ```markdown
   ## My work wiki (ignite)
   I keep an LLM-maintained work wiki at `<wiki_path>` — read its CLAUDE.md before touching it. When a session produces durable work, append a one-line entry to `<wiki_path>/wiki/log.md`.
   ```

5. Confirm setup is done, then run **KICKOFF** once against the fresh wiki.

---

## KICKOFF (every session)

1. `touch ~/.claude/.ignite.session_start` (session marker).
2. **Load the wiki** (in order): `<wiki_path>/CLAUDE.md` → `wiki/index.md` → `wiki/log.md` (last ~50 lines) → `wiki/learnings.md` (top 10 entries — apply these to today's work).
3. **Open loops:** read `wiki/open-loops.md`; surface overdue first, then due within 7 days.
4. **Output** one tight block:
   ```
   ## Today — <YYYY-MM-DD>
   **Wiki loaded**: CLAUDE.md, index, log, learnings
   **Last activity** (from log): <1–2 lines>
   **Learnings to apply**: <2–3 short>
   **Open loops**: <overdue / due-soon, or "none">
   **Suggested next**:
   1. <concrete action>
   2. <concrete action>
   3. <optional third>
   ```
5. **During the session**, append a one-line entry to `wiki/log.md` whenever durable work is produced (don't wait for session end). Capture corrections into `wiki/learnings.md`.

## Rules

- Reading the wiki + learnings is the whole point — not optional.
- Read-only orientation: the only writes during kickoff are the session marker and incremental log lines. Don't run scripts or start long tasks unless asked.
- Everything derives from `~/.claude/ignite-os.json` — no hard-coded paths.
- Summarize; don't dump file contents. Propose actions; don't ask "what do you want to do?"
