---
name: self-learning
description: >
  Capture a hard-won "golden path" from the current session as a reusable Agent
  Skill, so future sessions start already knowing it. Use it (1) right after
  non-trivial debugging, after working out a multi-step operational workflow, or
  after rediscovering project facts you didn't know up front — e.g. how to reach
  the dev/prod database, where credentials and env vars live, how to deploy, run
  migrations, or verify a change live; and (2) whenever the user says "remember
  this", "save this as a skill", "make a skill for this", "don't make me
  re-explain this next time", or otherwise wants a workflow preserved across
  sessions. Proactively recognize the moment even when unprompted: if a task took
  several attempts before it worked, used non-obvious tooling, or is likely to
  recur, harvest it without asking first. Spawns a fork agent that extracts the
  proven procedure into a new project-local or global SKILL.md.
license: MIT
metadata:
  author: kulaxyz
  version: "1.0"
---

# Self-learning: harvest golden paths into skills

This skill turns something you just figured out the hard way into a reusable
Agent Skill, so the next session — yours or a teammate's — starts already
knowing the proven route instead of rediscovering it from scratch.

It is a *meta-skill*: it doesn't do the work, it captures **how** work got done.

## Recognize the moment

Watch for these signals during normal work. Any one of them is a cue to harvest:

- A task only worked **after several attempts**, wrong turns, or a correction
  from the user. The successful path is worth more than the failures around it.
- You discovered **project-specific facts the agent didn't know up front**:
  where creds/env vars live, which selector or backend talks to a service, a
  non-obvious command, a required sequence, a gotcha that defies the obvious
  assumption.
- It's an **operational workflow likely to recur**: reach the dev/prod DB,
  deploy, run migrations, seed data, verify a change live, run one specific
  test path, rotate a key, tail the right logs.
- The user **signals it explicitly**: "remember this", "save this as a skill",
  "don't make me re-explain this next time".

**Act on the cue immediately — don't ask for permission first**, whether the
user requested it or you noticed it yourself. Harvest the skill, then tell the
user what you captured and where (step 5). They can always edit or delete it.

### Skill, memory, or skip?

Not every lesson deserves a whole skill — triage first, so you don't bloat the
skills list with one-liners:

- **A multi-step, reusable procedure or workflow** (how to deploy, reach the DB,
  run the migration dance, verify live) → harvest it as a **skill** using the
  procedure below.
- **A single standalone fact or one-line correction** (an env var name, a path,
  one gotcha) → if your harness has a lightweight memory/notes facility (e.g. a
  `MEMORY.md` index), record it **there** instead; a whole skill is overkill for
  a one-liner. With no such facility, make a small skill.
- **A genuinely one-off thing** unlikely to recur → skip it.

When you do harvest, capture the **failures too**, not just the win: the
approaches you ruled out and *why* often save more time next session than the
golden path itself.

### Promotion rule: don't enshrine guesses

A skill is authoritative — the next session trusts it without re-deriving it —
so hold promotion to a high bar. Only write a skill when **all three** hold:

1. **A passing check.** The path was actually verified — a test passed, the
   command exited clean, the repro reproduced, the build went green. Record what
   the check was. "Seemed to work" is not a passing check.
2. **A named failure pattern.** You can name the failure this path avoids or
   diagnoses (e.g. "stale build cache → phantom type errors"), not a vague
   "sometimes it breaks".
3. **At least one ruled-out dead-end.** A concrete approach you tried and
   eliminated, with the reason.

If any is missing, it isn't a skill yet — leave a tentative note in memory
(marked unverified) or skip it. This keeps confident guesses out of the skill
set.

## Harvest procedure

- [ ] 1. **Apply the promotion rule** (above). Passing check + named failure
      pattern + one ruled-out dead-end — or it isn't a skill: note it in memory
      or skip. Don't proceed on a confident guess.
- [ ] 2. **Choose scope and name yourself** using the heuristics below — don't
      stop to ask. Default to project scope; pick a clear, specific `name`.
- [ ] 3. **Dedupe.** Look for an existing skill to UPDATE rather than duplicate:
      `ls ~/.claude/skills` and `ls "$(git rev-parse --show-toplevel)/.claude/skills"`.
      Also glance at any `MEMORY.md` index — a fact already recorded there may
      just need a pointer, not a new skill.
- [ ] 4. **Distill the golden path from THIS conversation** before delegating —
      while it's fresh in your head: the exact working commands, file paths, env
      var names, the required order, and (just as important) the dead-ends to
      avoid. You'll hand this to the fork as raw material.
- [ ] 5. **Delegate the write** to a sub-agent that inherits this conversation
      (a fork, in Claude Code), or do it inline if your client has no such
      mechanism — see below. The conversation is the only place the golden path
      lives, so whoever writes it must have that context.
- [ ] 6. When the fork reports back, **relay the new skill's path** to the user
      and, in one line, what it captured.

### Scope: project vs global

- **Project** (`<repo>/.claude/skills/`): the path is specific to THIS codebase
  — its env vars, its deploy command, its schema, its quirks. Most harvested
  operational skills are project-scoped, and they ship to the team via git.
- **Global** (`~/.claude/skills/`): the path generalizes across projects — a
  personal tool, a cross-repo habit, or a workflow tied to your machine rather
  than to one repository.

When unsure, prefer **project** — an over-shared global skill triggers in repos
where its commands don't apply.

## Delegate the write (fork, or inline)

If your client can spawn a sub-agent that **inherits the current conversation**
(in Claude Code: a fork), use it — it keeps the harvesting work out of your main
context while still seeing the golden path. If it can't, run the same steps
**inline** in the main loop. The rules below are identical either way.

In Claude Code, use a **fork** (`subagent_type: "fork"`), *not* a fresh
`general-purpose` agent. The golden path only exists in this conversation's
context, and a fork inherits that context; a fresh agent would start blank and
have nothing to extract. Forks over-reach by default, so box it in tightly. Hand
it a prompt shaped like this (fill in the bracketed parts):

> You are a skill-harvesting fork. Your ONLY job is to write a new Agent Skill
> capturing the golden path we just worked out in this conversation:
> **[one-line description of the workflow]**.
>
> Hard rules:
> - Write ONLY under `[skills dir]/[skill-name]/`. Do NOT modify project
>   source, run builds, install anything, or resume the original task.
> - First read `[this-skill-dir]/references/skill-authoring.md` and
>   `[this-skill-dir]/assets/SKILL.template.md`, then author `SKILL.md` to that
>   spec, plus any `references/` or `assets/` files the procedure warrants.
> - Capture the PROCEDURE — commands, paths, the required order, gotchas — not a
>   one-off answer. Generalize so it works next time.
> - Capture the FAILURES too: the approaches we ruled out and why, so the next
>   session skips the dead-ends. Put them in a "What didn't work" section.
> - Enforce the promotion rule: the skill must record the passing check that
>   verified this path, name the failure pattern it addresses, and list at least
>   one ruled-out dead-end. If any is missing (e.g. nothing was actually
>   verified), STOP and report it isn't promotable — leave a tentative memory
>   note instead of writing the skill.
> - NEVER write secret VALUES (passwords, tokens, connection strings, API keys).
>   Record only WHERE to find them: the env var name, the selector function, the
>   MCP tool, the secret manager. Reproducing a secret into a skill file leaks it.
> - Self-validate before finishing (see the checklist in skill-authoring.md).
> - Report back: the absolute path you wrote and a one-line summary. Then STOP —
>   do not pick the original task back up.

## Gotchas

- **Secrets never go in a skill file.** Skills get committed and open-sourced.
  Point to *where* the secret lives; never reproduce the value. This is the
  single most important rule in this skill.
- **`name` must equal the directory name**, and be lowercase `a-z`/`0-9`/hyphens
  only — no leading, trailing, or doubled hyphens. A mismatch means the skill
  won't load.
- **Forks over-reach by default.** That's exactly why the prompt above forbids
  touching project source or resuming the task — keep the fork boxed to the
  skills directory.
- **Don't duplicate.** If a near-identical skill (or memory) already exists,
  update it instead of spawning a second one that competes to trigger.
- **Capture procedures, not answers.** "Join orders to customers for EMEA" is
  useless next time; "how to find the right tables and build the query" is the
  skill. See `references/skill-authoring.md`.
- **Keep `SKILL.md` tight** (< 500 lines, < ~5000 tokens). Push detail into
  `references/` and tell the reader *when* to load each file.

For the full authoring spec the fork follows, see
[references/skill-authoring.md](references/skill-authoring.md). The fill-in
template is [assets/SKILL.template.md](assets/SKILL.template.md).
