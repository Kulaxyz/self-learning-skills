# Skill authoring spec

Read this before writing a harvested skill. It is a condensed version of the
Agent Skills specification and best-practices, focused on what you need to
produce a good, well-triggering, safe skill. Source: https://agentskills.io

## Directory structure

```
<skill-name>/
├── SKILL.md          # required: YAML frontmatter + Markdown body
├── references/       # optional: docs the agent loads on demand
├── assets/           # optional: templates, schemas, static resources
└── scripts/          # optional: executable code the agent can run
```

`SKILL.md` MUST live at the skill root. Only the root `SKILL.md` is parsed as a
skill — files with frontmatter inside `references/`/`assets/` are inert (safe to
use as templates).

## Frontmatter

| Field           | Required | Rules |
|-----------------|----------|-------|
| `name`          | yes  | 1–64 chars, lowercase `a-z`/`0-9`/`-` only, no leading/trailing/`--`. **Must equal the directory name.** |
| `description`   | yes  | 1–1024 chars. Says **what it does AND when to use it**. Carries the entire triggering burden. |
| `license`       | no   | License name or a bundled file reference (e.g. `MIT`). |
| `compatibility` | no   | ≤500 chars. Only if there are real environment requirements (tools, network, runtime). Most skills omit it. |
| `metadata`      | no   | Arbitrary string→string map (e.g. `author`, `version`). |
| `allowed-tools` | no   | Space-separated pre-approved tools (experimental; support varies). Omit unless you have a reason. |

Minimal valid frontmatter:

```markdown
---
name: my-skill
description: What it does and when to use it.
---
```

## Writing the description (the most important field)

At startup the agent loads only `name` + `description` for every skill, and uses
the description to decide whether to load the body. Get this wrong and the skill
never fires (or fires when it shouldn't).

- **Imperative + "when".** "Use this skill when…", not "This skill does…".
- **What + when, both.** State the capability and the trigger situations.
- **Be pushy about triggers.** List the contexts it applies to, including when
  the user won't name the domain: "…even if they don't mention 'X'."
- **Match user intent, not internals.** Describe what the user is trying to do.
- **Concise.** A few sentences to a short paragraph. Hard limit 1024 chars.

```yaml
# weak
description: Process CSV files.

# strong
description: >
  Analyze CSV/TSV/Excel data — summary stats, derived columns, charts, cleaning.
  Use when the user has a tabular data file and wants to explore, transform, or
  visualize it, even if they don't explicitly say "CSV" or "analysis."
```

## Body content

No required format, but favor this shape:

1. One or two lines on what the skill is for.
2. **Procedure** — numbered/checklist steps, with a clear default at each choice.
3. A short worked example (input → command → output) when it helps.
4. **Gotchas** — see below; often the highest-value part.

Calibrate prescriptiveness to fragility:
- **Be exact** for fragile/destructive/order-dependent steps ("run exactly this
  command, don't add flags").
- **Give freedom + explain why** where several approaches are valid.
- **Provide a default, not a menu.** Name one tool/approach; mention an
  alternative briefly as an escape hatch.
- **Procedures over declarations.** Teach how to approach the class of problem,
  not the answer to one instance — that's what makes it reusable.

### Add what the agent lacks, omit what it knows

Spend tokens only on what the agent wouldn't get right on its own: project
conventions, the specific commands/paths/tools, and non-obvious edge cases.
Don't explain what a database or a deploy is. For each line ask: "Would the
agent get this wrong without it?" If no, cut it.

### Gotchas section (high value)

Concrete corrections to mistakes the agent *will* make otherwise — not generic
advice. Keep these in `SKILL.md` so they're read before the situation arises.

```markdown
## Gotchas
- The `users` table uses soft deletes — queries need `WHERE deleted_at IS NULL`.
- The `/health` endpoint returns 200 even when the DB is down; use `/ready`.
- Creds live in env var `FOO_TOKEN` (see `lib/clients/foo-real.ts`), never in code.
```

### Useful patterns (use the ones that fit)

- **Checklist** for multi-step workflows with dependencies.
- **Validation loop**: do the work → run a check → fix → repeat until it passes.
- **Plan-validate-execute** for batch/destructive ops.
- **Output template** when a specific format is required (agents pattern-match
  templates better than prose).

## Progressive disclosure

- Keep `SKILL.md` under **500 lines / ~5000 tokens**.
- Move long reference material to `references/`, templates to `assets/`,
  reusable code to `scripts/`.
- Reference them with **relative paths**, one level deep, and tell the agent
  *when* to load each: "Read `references/api-errors.md` if the API returns a
  non-200." A generic "see references/" defeats the purpose.

## Secrets safety (non-negotiable)

Harvested skills get committed and often open-sourced. **Never write a secret
value** — no passwords, tokens, connection strings, API keys, or private
endpoints. Record only **where to find it**: the env var name, the selector
function, the MCP tool, the secret manager / vault entry. If you catch yourself
pasting a value, replace it with its source.

## Self-validation checklist (run before finishing)

- [ ] **Promotion rule met**: the skill records a passing check that verified the
      path, names the failure pattern it addresses, and lists ≥1 ruled-out
      dead-end. If any is missing, this shouldn't be a skill — stop and report it.
- [ ] `SKILL.md` exists at the skill root.
- [ ] `name` matches the directory name and the regex (lowercase, hyphen rules).
- [ ] `description` is non-empty, ≤1024 chars, and states what + when.
- [ ] Body is a generalized **procedure**, not a one-off answer.
- [ ] No secret values anywhere in the skill — only pointers to them.
- [ ] `SKILL.md` is under ~500 lines; long material is in `references/`/`assets/`.
- [ ] Relative file references are correct and one level deep.

Optional, if `skills-ref` is installed:
`skills-ref validate <path-to-skill>` checks frontmatter and naming.
