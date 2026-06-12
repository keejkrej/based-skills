---
name: memory
description: >-
  Maintain AGENTS.md (purpose, rules, tech stack) and docs/agent/ notes so
  context survives sessions. Use when starting work in a repo, syncing AGENTS.md
  from config and chat, recording decisions or preferences, resuming prior tasks,
  or when the user mentions agent memory, notes, or AGENTS.md.
---

# Memory

**AGENTS.md** is the only file humans review regularly — agents write and update it.
**docs/agent/** holds session/working context; do not duplicate AGENTS.md there.

## On activate (start of session or when user invokes memory)

1. Read `AGENTS.md` — follow **Purpose**, **Rules**, and **Tech stack** before planning or editing
2. Read relevant `docs/agent/*.md`
3. Run **AGENTS.md audit** (below) — refresh sections when config, README, or chat changed
4. Self-check before finishing: re-read **Purpose** and **Tech stack**; run repo lint on touched files

## AGENTS.md audit

1. Read repo config to see what's in use — check when relevant: `package.json`, `.oxlintrc.json`, `vite.config.*`, `tsconfig.json`, `bunfig.toml`, `pyproject.toml`, `turbo.json`, README, existing **Purpose**
2. Read the **techstack** skill — pick the domain(s) that match this repo; read linked domain file(s) when stack is non-trivial
3. Build **Tech stack** from techstack defaults that apply here + repo-specific facts config alone doesn't convey

### Build **Tech stack** from techstack

Write bullets an agent needs **before** opening config files — name the stack and conventions, not a config dump.

Include from techstack (when this repo uses them):

- runtime / package manager (Bun, uv, Cargo, …)
- primary frameworks and libraries (React, Effect, FastAPI, …)
- lint, format, typecheck tooling (oxlint, Ruff, …)
- architectural conventions agents violate by default (Effect for IO, atoms for state, no manual memoization with React Compiler, extensionless TS imports, …)
- contract/boundary approach when cross-surface (Effect Schema, OpenAPI, …)

Add repo-specific bullets techstack doesn't cover:

- package boundaries, monorepo layout, where shared UI or contracts live
- paths or policies unique to this repo
- user preferences or team norms not in techstack

Exclude:

- full script/command inventory — one line naming the toolchain is enough
- verbatim domain-doc prose — distill what applies here
- stale bullets after stack migration — replace, don't accumulate

Keep **Tech stack** concise — prefer 5–12 bullets; drop bullets that became wrong or redundant.

**Update only** `## Tech stack` between `<!-- memory:techstack-start -->` and `<!-- memory:techstack-end -->`.
**Update** **Purpose** and **Rules** when chat or README reveals durable intent — do not wait for the user to edit manually.
**Do not** store secrets, tokens, or private URLs in AGENTS.md.

### Example **Tech stack** bullets

- Bun + Vite + React 19 + Tailwind v4 + coss-ui + Effect Atom + TanStack Router
- Effect for client/server IO — not raw `fetch` or ad hoc Promises in components
- oxfmt + oxlint; React Compiler on — no `useMemo`, `useCallback`, or `memo`
- coss primitives in `packages/ui/src/components/ui/` — not in apps
- when unsure on scope → see **Purpose** non-goals

## Purpose

Guides product and scope decisions — what to build, for whom, what to skip.

- Top of AGENTS.md, above **Rules**
- Update from README, chat, or user statements — use placeholder only if truly unknown: `(what we're building and for whom)`
- Do not invent product intent without repo signals

Include when known: audience, core problem, non-goals, tradeoff priorities, north star for scope.

## Rules

Agent workflow and repo-specific behavior — update when conventions change.

## AGENTS.md layout (ensure on first audit if missing)

```markdown
# AGENTS.md

Humans review this file regularly. Agents maintain it via the memory skill.

## Purpose

- (product intent, users, non-goals, tradeoff priorities)

## Rules

- (agent workflow, repo conventions)

## Tech stack

<!-- memory:techstack-start -->
- (relevant stack from techstack skill + repo-specific conventions)
<!-- memory:techstack-end -->

## Context

- Durable notes: `docs/agent/` (memory skill)
```

## docs/agent/ — when to read

- User references prior work, decisions, or "what we decided"
- Task spans multiple sessions or agents
- Unclear conventions — check notes before guessing

## docs/agent/ — when to write

- In-progress work, open questions, feature-specific context
- Facts not yet promoted to AGENTS.md
- Promote to **Purpose**, **Rules**, or **Tech stack** when durable — then trim duplicate from `docs/agent/`

## Do not store in docs/agent/

- Secrets, tokens, credentials, or private URLs
- Ephemeral debug output or one-off command results
- Duplicates of AGENTS.md — promote instead
- Large logs or pasted code — summarize and point to paths

## docs/agent/ layout

- Root: `docs/agent/`
- One topic per file; kebab-case names (`open-questions.md`, `feature-x.md`)
- Optional dated entries: `## YYYY-MM-DD`
- Keep files short; split past ~100 lines

## Format

- Concise bullet points
- Lead with facts and decisions, not narrative
- Include file paths when they disambiguate behavior
- Mark uncertainty in docs/agent/: `open:` / `decided:` / `superseded:`

## Conflict resolution

- Config files win over stale AGENTS.md prose — refresh **Tech stack** from config + techstack when they diverge
- Transient intent stays in `docs/agent/` until promoted
