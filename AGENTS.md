# Self-learning (for AI coding agents)

This file makes any coding agent **self-improving**: recognize a hard-won
"golden path" during a task and persist it so the next session starts already
knowing it, instead of rediscovering how to reach the DB, where the creds live,
how to deploy, or how to verify a change live.

It works with any agent that reads a standing instructions file (Codex, Zed,
Aider, Gemini CLI, …). Richer, tool-native installs exist too — a Claude Code
**skill** (`skills/self-learning/SKILL.md`) and a **Cursor rule**
(`.cursor/rules/self-learning.mdc`); see the README. This file is the portable,
lowest-common-denominator version.

## The loop

**1. Recognize the moment.** Any one of these is a cue:
- a task only worked after several attempts, wrong turns, or a correction;
- you discovered project facts you didn't know up front — where creds/env vars
  live, a non-obvious command, a required sequence, a gotcha;
- an operational workflow likely to recur (reach the dev/prod DB, deploy, run
  migrations, seed data, verify live, tail the right logs);
- the user says "remember this" / "don't make me re-explain this next time".

Act on the cue immediately — **don't ask permission first**. Capture it, then
tell the user what you saved and where. They can always edit or delete it.

**2. Capture it where your tool auto-loads knowledge next session:**
- Claude Code / any Agent Skills client → a new `skills/<name>/SKILL.md`
- Cursor → a new `.cursor/rules/learned/<name>.mdc`
- Otherwise → append a dated entry under [Learned](#learned) below, or to your
  project's notes/memory file.

Capture the **procedure** (commands, paths, the required order, gotchas) — not a
one-off answer — and the **failures** too: the approaches you ruled out and why,
so next time skips the dead-ends.

**3. Reuse.** Next session the persisted entry loads automatically (by skill/rule
description, or because this file is always read) and you start from the golden
path.

## Rules

- **Never write secret values** — no tokens, passwords, connection strings, or
  API keys. Record only *where* a secret lives (env var name, config/selector,
  secret manager). Reproducing a secret into a shared file leaks it.
- **A one-line fact or correction** → put it in lightweight notes/memory, not a
  whole rule or skill.
- **A genuine one-off** unlikely to recur → skip it.
- **Capture procedures, not answers** — teach how to approach the class of
  problem, so it generalizes next time.

## Learned

<!-- When no richer mechanism is available, append dated golden-path entries here.
     Format: ### YYYY-MM-DD — <title>  /  **Goal**, **Steps**, **Gotchas**,
     **What didn't work**. Keep secrets out — point to where they live. -->
