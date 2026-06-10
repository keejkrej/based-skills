---
name: memory
description: >-
  Read and write persistent agent notes in docs/agent/ so context survives
  sessions. Use when starting work in a repo, learning durable project facts,
  recording decisions or preferences, resuming prior tasks, or when the user
  mentions agent memory, notes, or context that should persist.
---

# Memory

Persist durable project context in `docs/agent/` — not in chat alone.

## When to read

- Start of a session in this repo, before planning or editing
- User references prior work, decisions, or "what we decided"
- Task spans multiple sessions or agents
- Unclear conventions — check notes before guessing

## When to write

- User states a durable preference, constraint, or decision
- You learn non-obvious repo facts (layout, workflows, gotchas)
- You finish a chunk of work worth resuming later
- You correct a wrong assumption future agents might repeat

## Do not store

- Secrets, tokens, credentials, or private URLs
- Ephemeral debug output or one-off command results
- Duplicates of AGENTS.md, README, or skill content — link instead
- Large logs or pasted code — summarize and point to paths

## Layout

- Root: `docs/agent/`
- One topic per file; kebab-case names (`stack-decisions.md`, `open-questions.md`)
- Optional dated entries at top: `## YYYY-MM-DD` under a stable file
- Keep files short; split when a file grows past ~100 lines

## Format

- Concise bullet points — same style as skills
- Lead with facts and decisions, not narrative
- Include file paths and commands when they matter
- Mark uncertainty: `open:` / `decided:` / `superseded:`

## Workflow

- Read relevant `docs/agent/*.md` before acting
- Update existing files in place when the topic matches
- Create a new file only for a distinct topic
- On conflict with repo docs, trust committed docs; note the discrepancy in memory
- Do not edit README.md or AGENTS.md unless the user asks

## Examples

```markdown
## decided:
- API errors use Effect tagged errors, not thrown exceptions — see packages/api/src/errors.ts

## open:
- Whether to split auth into its own package
```
