# Target Tree Template

Copy into `.refactor/TARGET_TREE.md` in the target repo. Phase plan: `.refactor/plans/phase-2-target-tree.md`.

```markdown
# Target Code Tree

## Meta

- **Based on:** `.refactor/DIAGNOSIS.md` ([date or revision])
- **Pattern:** [MVC, MVVM, MVP, layered, hexagonal, clean/onion, modular monolith, hybrid]
- **Scope:** [same scope as diagnosis]
- **Date:** [YYYY-MM-DD]
- **Author:** [agent or human]

## Intent

[2–4 sentences: what this layout optimizes for and how it addresses top diagnosis clusters]

## Target tree

<!--
  Use the project's real root. Annotate pattern roles inline.
  Prefer a tree the team can diff against the repo.
-->

```
[repo-root]/
├── [presentation-layer]/     # [pattern role]
│   ├── ...
├── [application-layer]/      # [pattern role]
│   ├── ...
├── [domain-layer]/           # [pattern role]
│   ├── ...
├── [infrastructure-layer]/   # [pattern role]
│   ├── ...
└── [bootstrap-or-platform]/  # composition, config, wiring
```

## Role map

| Path | Pattern role | Owns | Phase 4 | Phase 5 |
|------|--------------|------|---------|---------|
| `[path]` | [role] | [responsibilities] | pending | pending |

## Dependency rules

- [Outer → inner, feature isolation, port direction, etc.]
- [What must not import what]

## Current → target mapping

| Current location | Target location | Finding IDs | Notes |
|------------------|-----------------|-------------|-------|
| `path/today` | `path/target` | F1, F3 | [move, split, merge, rename] |

## New units to introduce

| Path | Purpose | Replaces or splits |
|------|---------|-------------------|
| `new/path` | [why it exists] | [current god unit or cluster] |

## Unchanged

- [Paths or boundaries that stay as-is and why]

## Deferred layout changes

- [Target-tree items intentionally postponed]

## Open decisions

- [Naming, boundary, or pattern choice needing user input]

## Revision log

| Date | Change |
|------|--------|
| [YYYY-MM-DD] | Initial target tree |
```

## Authoring rules

- Derive the tree from the **Intended architecture** in `DIAGNOSIS.md` and [architecture-patterns.md](architecture-patterns.md).
- Name folders by **responsibility**, not by current filenames.
- Keep the tree **achievable in Phase 3 separation slices** — avoid speculative folders with no mapped current code.
- Every top diagnosis cluster should map to at least one target path or mapping row.
- Do not list every file; show **stable module boundaries** the refactor will enforce.
- When the pattern is hybrid, document which subtree uses which pattern.
- **Role map** is the Phase 4 walk list — include every branch and leaf. Phase 4 status tracks contracts; Phase 5 tracks wiring.
