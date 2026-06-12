# AGENTS.md

Humans review this file only. Agents maintain **Tech stack** via the memory skill.

## Purpose

- personal skills repo — curated agent skills synced to Cursor/Claude, not a runtime app
- users: me, when coding with agents; skills must be concise and trigger reliably
- optimize for small, focused skills over monolithic guides
- non-goals: app scaffolding in this repo, duplicating global ~/.agents skills here
- when unsure: prefer editing repo skills over adding one-off chat instructions

## Rules

- always edit skills within the repo, don't bother the global skills in ~/.agents
- all skills be written in concise bullet points
- before editing code in another repo: read that repo's **Purpose** and **Tech stack**; run lint on touched files before finishing

## Tech stack

<!-- memory:techstack-start -->
- skills repo — markdown only; no application runtime or linter config to audit
- package manager / lint / format: n/a
<!-- memory:techstack-end -->

## Context

- durable session notes: `docs/agent/` (memory skill)
