# self-learning-skills

**A self-improving skill for AI coding agents.** Works with Claude Code, Cursor,
and any agent that reads an `AGENTS.md` / standing-instructions file.

Every session you do hard debugging or rediscover the same thing — *how do I
reach the prod DB? where do the creds live? what's the deploy command? how do I
verify this live?* — and that hard-won knowledge evaporates when the session
ends. The next session starts from zero and re-learns it.

**self-learning** fixes that. It teaches your agent to recognize the moment it
has just earned a reusable **golden path** and persist it where the tool will
auto-load it next time — so the next session starts already knowing the route
instead of rediscovering it.

It's a *meta-skill*: it doesn't do the work, it captures **how** the work got
done — including the **failures**, since skipping a known dead-end next session
is often worth more than the win itself.

## The loop (same everywhere)

1. **Recognize the moment** — a task that only worked after several tries, a
   non-obvious command, a project fact you didn't know up front, an operational
   workflow likely to recur, or you simply saying *"remember this"*.
2. **Capture it, no prompt needed** — it acts on the cue immediately, picks the
   scope/name itself, and tells you afterward. The *procedure* is captured (not
   a one-off answer), plus a "what didn't work" note.
3. **Reuse** — next session the entry loads automatically, by skill/rule
   description or because the instructions file is always read.

What differs per tool is only *where* knowledge is persisted and *how* it's
auto-loaded:

| Tool | Persists golden paths to | Auto-loads via |
|---|---|---|
| Claude Code / Agent Skills clients | a new `skills/<name>/SKILL.md` | skill description matching |
| Cursor | a new `.cursor/rules/learned/<name>.mdc` | rule description / globs |
| Codex, Zed, Aider, … | `AGENTS.md` (or project notes/memory) | always-read instructions |

## Install

### `npx` — recommended (works with 70+ agents)

Uses the community [`skills`](https://github.com/vercel-labs/skills) CLI, which
installs into whatever agents it detects — Claude Code, Cursor, Codex, Cline,
OpenCode, and more:

```bash
npx skills add kulaxyz/self-learning-skills                 # this project (auto-detects agents)
npx skills add kulaxyz/self-learning-skills -g              # global — all your projects
npx skills add kulaxyz/self-learning-skills -a claude-code  # a specific agent
```

Try it once without installing:

```bash
npx skills use kulaxyz/self-learning-skills --skill self-learning | claude
```

### Claude Code plugin

```
/plugin marketplace add kulaxyz/self-learning-skills
/plugin install self-learning@self-learning-skills
```

### Manual

<details>
<summary>Copy the files into place yourself</summary>

```bash
git clone https://github.com/kulaxyz/self-learning-skills

# Claude Code — global (or into a project's .claude/skills/ to share via git)
cp -R self-learning-skills/skills/self-learning ~/.claude/skills/

# Cursor — auto-loads .cursor/rules/ (harvested rules land in .cursor/rules/learned/)
mkdir -p .cursor/rules
cp self-learning-skills/.cursor/rules/self-learning.mdc .cursor/rules/

# Any AGENTS.md agent (Codex, Zed, Aider, Gemini CLI, …)
curl https://raw.githubusercontent.com/kulaxyz/self-learning-skills/main/AGENTS.md >> AGENTS.md
```
</details>

## Triage: skill, memory, or skip?

It won't bloat your config with one-liners. Each lesson is routed:

| Lesson | Where it goes |
|---|---|
| A multi-step, reusable **procedure/workflow** | a new skill / rule |
| A single **fact or one-line correction** | lightweight notes/memory (e.g. a `MEMORY.md`) |
| A genuine **one-off** | skipped |

## Safety

Harvested skills/rules get committed and shared, so this is built to **never
write secret values** — no passwords, tokens, connection strings, or API keys.
It records only *where* to find a secret (env var name, a client/selector
function, an MCP tool, a secret manager). Reproducing a secret into a shared
file leaks it.

## Repo layout

```
self-learning-skills/
├── AGENTS.md                          # generic, cross-tool version of the loop
├── skills.sh.json                     # registry manifest for `npx skills` / skills.sh
├── .claude-plugin/
│   └── marketplace.json               # Claude Code plugin manifest
├── skills/
│   └── self-learning/                 # Agent Skills standard (Claude Code + clients)
│       ├── SKILL.md                   # recognize-the-moment + harvest procedure
│       ├── references/
│       │   └── skill-authoring.md     # condensed spec the writer loads to author a good skill
│       └── assets/
│           └── SKILL.template.md      # fill-in template for harvested skills
└── .cursor/
    └── rules/
        ├── self-learning.mdc          # Cursor adapter (always-applied rule)
        └── learned/                   # harvested Cursor rules land here
```

Built on the open [Agent Skills](https://agentskills.io) standard.

## License

[MIT](LICENSE) © kulaxyz
