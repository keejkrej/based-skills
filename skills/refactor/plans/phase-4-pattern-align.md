# Phase 4 Plan Template

Copy into `.refactor/plans/phase-4-pattern-align.md` before executing Phase 4.

```markdown
# Phase 4 — Pattern align

## Meta

- **Based on:** `TARGET_TREE.md`, `PROGRESS.md` Separation notes
- **Date:** [YYYY-MM-DD]

## Branch walk order

1. `[path]` — [pattern role]
2. `[path]` — [pattern role]
...

## Per branch

<!-- Repeat per role-map entry -->

### `[path]` — [pattern role]

- **Finding IDs:** [F…]
- **Role fit tasks:** [moves out, port extractions]
- **Internal shape tasks:** [dedupe, split files, bestpractice items]
- **Contracts to define:** [ports, interfaces, commands, signals, public API]
- **Awaiting Phase 5:** [edges that must not be wired yet]
- **Done when:** `contracts defined`

## Exit criteria

- [ ] Every in-scope branch `contracts defined` or `deferred`
- [ ] No cross-branch wiring (DI, routes, events) in this phase
- [ ] `PROGRESS.md` **Pattern align** updated

## Revision log

| Date | Change |
|------|--------|
| [YYYY-MM-DD] | Initial plan |
```
