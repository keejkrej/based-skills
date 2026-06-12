---
name: memory
description: >-
  Maintain AGENTS.md (purpose, rules, easy-to-miss stack notes) and docs/agent/
  notes so context survives sessions. Use when starting work in a repo, syncing
  AGENTS.md from config and chat, recording decisions or preferences, resuming
  prior tasks, or when the user mentions agent memory, notes, or AGENTS.md.
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

Read config to find **gaps and non-obvious rules** — do not copy config inventory into **Tech stack**.

Check when relevant: `package.json`, `.oxlintrc.json`, `vite.config.*`, `tsconfig.json`, `bunfig.toml`, `pyproject.toml`, `turbo.json`, README, existing **Purpose**.

### Write to **Tech stack** only when easy-to-miss

Include:

- conventions agents violate despite config (e.g. React Compiler on → do not add `useMemo`/`useCallback`/`memo`)
- policy enforced in config but counter to model defaults — cite path, state the don't
- architectural choices not obvious from file tree (package boundaries, where UI primitives live)
- user preferences or team norms not encoded in linters
- missing expected policy on greenfield repos (e.g. no `bunfig.toml` release-age on Bun repo)

Exclude — agents can read these themselves:

- package manager, framework, or tool names discoverable from `package.json` / lockfiles
- lint/format/typecheck commands and config filenames alone
- tsconfig options visible in `tsconfig.json`
- anything fully and obviously enforced by CI/lint with no behavioral surprise

Keep **Tech stack** short — prefer 3–8 bullets; drop bullets that became obvious or redundant.

**Update only** `## Tech stack` between `<!-- memory:techstack-start -->` and `<!-- memory:techstack-end -->`.
**Update** **Purpose** and **Rules** when chat or README reveals durable intent — do not wait for the user to edit manually.
**Do not** store secrets, tokens, or private URLs in AGENTS.md.

### Example **Tech stack** bullets (when applicable)

- do not use `useMemo`, `useCallback`, or `memo` — React Compiler + `.oxlintrc.json` `no-restricted-imports`
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
- (easy-to-miss conventions only — not config inventory)
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

- Config files win over stale AGENTS.md prose
- Fix **Tech stack** to match config; keep only the behavioral rule agents need, not a config dump
- Transient intent stays in `docs/agent/` until promoted
