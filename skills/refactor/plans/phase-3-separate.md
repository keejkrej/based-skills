# Phase 3 Plan Template

Copy into `.refactor/plans/phase-3-separate.md` before executing Phase 3.

```markdown
# Phase 3 — Separate

## Meta

- **Based on:** `DIAGNOSIS.md`, `TARGET_TREE.md`
- **Date:** [YYYY-MM-DD]

## Slices

<!-- One block per physical move -->

### S[N] — [short name]

- **Finding IDs:** F1, F3
- **Source:** `path/to/current`
- **Target:** `path/in/target/tree`
- **Tactic:** [same-package split | partial | mixin | cut-and-paste | lift-and-shift]
- **Intact split glue:** [what stays in source file, if any]
- **Expected broken refs:** [cross-branch — Phase 4 contracts, Phase 5 wire]
- **Done when:** [code physically at target; behavior unchanged]

## Order

1. S1 — [reason]
2. S2 — ...

## Exit criteria

- [ ] All planned slices complete or deferred
- [ ] `PROGRESS.md` **Separation** updated
- [ ] Broken refs recorded for Phases 4–5

## Revision log

| Date | Change |
|------|--------|
| [YYYY-MM-DD] | Initial plan |
```
