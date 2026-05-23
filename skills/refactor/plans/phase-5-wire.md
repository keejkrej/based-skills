# Phase 5 Plan Template

Copy into `.refactor/plans/phase-5-wire.md` before executing Phase 5.

```markdown
# Phase 5 — Wire

## Meta

- **Based on:** `TARGET_TREE.md`, Phase 4 contracts in `PROGRESS.md`
- **Date:** [YYYY-MM-DD]

## Wiring tasks

<!-- One block per integration edge -->

### W[N] — [caller] → [contract]

- **From:** `[path]`
- **To contract:** [port / interface / signal / public API]
- **Mechanism:** [import, DI, route, event, config]
- **Glue to remove:** [Phase 3 stubs, duplicates]
- **Verification:** [build/test scope]
- **Done when:** [edge works end-to-end]

## Order

1. W1 — [adapters / infrastructure first]
2. W2 — ...

## Exit criteria

- [ ] All wiring tasks done or deferred
- [ ] Scope builds and tests pass (or failures documented)
- [ ] No remaining `# TODO wire` stubs in scope
- [ ] `PROGRESS.md` **Wiring** updated

## Revision log

| Date | Change |
|------|--------|
| [YYYY-MM-DD] | Initial plan |
```
