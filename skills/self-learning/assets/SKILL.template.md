---
name: REPLACE-with-skill-name-matching-this-directory
description: >
  REPLACE. One short paragraph (≤1024 chars). Say WHAT the skill does AND WHEN
  to use it. Use imperative phrasing ("Use this skill when…"), be pushy about
  trigger situations, and list contexts even when the user won't name the
  domain. Match user intent, not internals.
license: MIT
metadata:
  author: REPLACE
  version: "1.0"
---

# REPLACE — title of the golden path

One or two sentences: what this captures and the situation it's for.

<!-- Promotion rule: fill BOTH lines. If you can't, this shouldn't be a skill. -->
**Failure pattern:** REPLACE — the failure this path avoids or diagnoses.
**Verified by:** REPLACE — the passing check that confirmed it (test passed, clean
exit, green build, reproduced repro). Not "seemed to work".

## When to use this

- REPLACE: the concrete trigger(s) — the recurring task or situation this serves.

## Procedure

<!-- Teach the METHOD, not a one-off answer. Be exact for fragile/ordered steps;
     give freedom (and say why) where several approaches work. Default, not menu. -->

- [ ] 1. REPLACE — first step (exact command / path if fragile).
- [ ] 2. REPLACE — next step, with the required order if it matters.
- [ ] 3. REPLACE — how to verify it worked.

### Example

```
REPLACE — a short, real input → command → expected output, if it helps.
```

## Gotchas

<!-- The highest-value section. Concrete corrections to mistakes that WILL happen
     otherwise — not generic advice. Record where secrets live, never their value. -->

- REPLACE — a non-obvious fact that defies the reasonable assumption.
- REPLACE — where creds/config live (env var name, selector fn, MCP tool, vault),
  NEVER the secret value itself.

## What didn't work

<!-- Required by the promotion rule: list at least one approach you ruled out and
     WHY, so the next session skips the dead-end instead of re-discovering it. -->

- REPLACE — approach that looked right but failed, and the reason.

<!-- Optional: move long reference material to references/ and templates to
     assets/, and tell the reader WHEN to load each. Keep this file < 500 lines. -->
