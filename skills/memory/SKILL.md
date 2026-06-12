---
name: memory
description: >-
  Maintain AGENTS.md (purpose, tech stack, self-check rules) and docs/agent/
  notes so context survives sessions. Use when starting work in a repo, syncing
  stack from config, recording decisions or preferences, resuming prior tasks, or
  when the user mentions agent memory, notes, or AGENTS.md.
---

# Memory

**AGENTS.md** is the only file humans review regularly — agents keep it current.
**docs/agent/** holds durable session context agents read/write; do not duplicate AGENTS.md there.

## On activate (start of session or when user invokes memory)

1. Read `AGENTS.md` — follow **Purpose**, **Rules**, and **Tech stack** before planning or editing
2. Read relevant `docs/agent/*.md`
3. Run **AGENTS.md audit** (below) — update **Tech stack** when config or preferences changed
4. Self-check before finishing: re-read **Purpose** for product tradeoffs; re-read **Tech stack**; run repo lint on touched files

## AGENTS.md audit

Inspect repo config — do not guess; derive bullets from files that exist.

| Signal | Look at |
|--------|---------|
| JS/TS runtime, scripts | root `package.json`, workspace packages |
| Lint/format | `.oxlintrc.json`, `oxfmt.json`, `ruff.toml`, `clippy` in `Cargo.toml` |
| Types | `tsconfig.json`, `[tool.ty]`, `mypy.ini` |
| React | `vite.config.*`, `@vitejs/plugin-react`, `babel-plugin-react-compiler`; oxlint `no-restricted-imports` on `useMemo`/`useCallback`/`memo` |
| Python | `pyproject.toml`, `uv.lock` |
| Monorepo | `turbo.json`, workspace layout |

**Update only the `## Tech stack` section** (between `<!-- memory:techstack-start -->` and `<!-- memory:techstack-end -->`).

Each bullet: **rule** → **why or enforced-by path** → **do / don't** when non-obvious.

Examples to emit when detected:

- React Compiler on + oxlint blocks manual memo → "Do not use `useMemo`, `useCallback`, or `memo` — see `.oxlintrc.json`"
- Extensionless imports → cite oxlint `import/extensions`
- User preference in chat or `docs/agent/` → merge into **Tech stack** if durable

**Preserve** **Purpose** and **Rules** — human-maintained; agents read, never overwrite unless the user asks.
**Preserve** content outside the techstack markers unless the user asks.
**Do not** store secrets, tokens, or private URLs in AGENTS.md.

## Purpose (human-maintained)

Guides product and scope decisions — what to build, for whom, and what to skip.

- Place at top of AGENTS.md, above **Rules**
- User writes; agents read before feature or UX decisions
- If missing on first audit, add the section with `(fill in — what we're building and for whom)` placeholder only — do not invent product intent
- Suggest updates to the user when chat reveals durable product direction; do not write it yourself

Include when known:

- what we're building and for whom
- core problem and non-goals
- tradeoff priorities (e.g. "minimal UI > feature breadth")
- north star for scope ("when unsure, prefer X over Y")

## AGENTS.md layout (ensure on first audit if missing)

```markdown
# AGENTS.md

Humans review this file only. Agents maintain **Tech stack** via the memory skill.

## Purpose

- (human-maintained — product intent, users, non-goals, tradeoff priorities)

## Rules

- (repo-specific agent behavior — user may edit)

## Tech stack

<!-- memory:techstack-start -->
- (agent-maintained — audited from config)
<!-- memory:techstack-end -->

## Context

- Durable notes: `docs/agent/` (memory skill)
```

## docs/agent/ — when to read

- User references prior work, decisions, or "what we decided"
- Task spans multiple sessions or agents
- Unclear conventions — check notes before guessing

## docs/agent/ — when to write

- User states a durable product direction → suggest **Purpose** update to user; do not write it unless asked
- User states a durable stack preference not yet in **Tech stack** → write note, then run AGENTS.md audit to promote if stack-level
- Non-obvious repo facts (layout, workflows, gotchas)
- Chunk of work worth resuming later
- Correct a wrong assumption future agents might repeat

## Do not store in docs/agent/

- Secrets, tokens, credentials, or private URLs
- Ephemeral debug output or one-off command results
- Duplicates of AGENTS.md **Purpose** or **Tech stack** — promote to AGENTS.md instead
- Large logs or pasted code — summarize and point to paths

## docs/agent/ layout

- Root: `docs/agent/`
- One topic per file; kebab-case names (`open-questions.md`, `feature-x.md`)
- Optional dated entries: `## YYYY-MM-DD`
- Keep files short; split past ~100 lines

## Format (AGENTS.md tech stack + docs/agent/)

- Concise bullet points
- Lead with facts and decisions, not narrative
- Include file paths and commands when they matter
- Mark uncertainty in docs/agent/: `open:` / `decided:` / `superseded:`

## Conflict resolution

- Config files win over prose
- If AGENTS.md disagrees with config, fix **Tech stack** to match config
- Note transient intent in `docs/agent/` until audit promotes it
