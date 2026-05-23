# Diagnosis Template

Copy into `.refactor/DIAGNOSIS.md` in the target repo. Phase plan: `.refactor/plans/phase-1-diagnose.md`.

```markdown
# Refactor Diagnosis

## Meta

- **Scope:** [repo / app / package / feature — paths included]
- **Stack:** [languages, frameworks, build tools — when relevant]
- **Intended architecture:** [MVC, MVVM, MVP, layered, hexagonal, clean/onion, modular monolith, hybrid, unknown]
- **Excluded:** [paths or concerns out of scope]
- **Date:** [YYYY-MM-DD]
- **Author:** [agent or human]

## Summary

[2–5 sentences: overall structural health, top risks, recommended refactor theme]

## Findings

<!-- Repeat one block per anti-pattern instance or cluster -->

### [ID] [Anti-pattern name] — [Severity: critical / high / medium / low]

- **Locations:** `path/to/module`, `other/path`
- **Evidence:** [dependencies, size, duplicate logic, mixed concerns — be specific]
- **Impact:** [what breaks or slows down because of this]
- **Suggested direction:** [extract module, dedupe, invert dependency — not a full target tree]
- **Dependencies:** [findings or slices that must land first, if any]

## Clusters

Group related finding IDs for planning:

| Cluster | Finding IDs | Theme |
|---------|-------------|-------|
| [name] | F1, F2 | [one-line theme] |

## Deferred

- [Finding or area left alone and why]

## Open questions

- [Ambiguity that blocks the target tree or planning]

## Revision log

| Date | Change |
|------|--------|
| [YYYY-MM-DD] | Initial diagnosis |
```

## Severity guide

| Severity | Use when |
|----------|----------|
| **critical** | Blocks safe change, hides bugs, or causes wrong-layer dependencies across features |
| **high** | Large duplication, god files, or mixed concerns in hot paths |
| **medium** | Local cohesion problems, inconsistent wiring, reinvented infrastructure |
| **low** | Naming, minor duplication, style drift with clear owner |
