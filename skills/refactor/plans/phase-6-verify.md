# Phase 6 Plan Template

Copy into `.refactor/plans/phase-6-verify.md` before executing Phase 6.

```markdown
# Phase 6 — Verify

## Meta

- **Based on:** all prior plans and deliverables
- **Date:** [YYYY-MM-DD]

## Checks

### Layout

- [ ] Repo tree matches `TARGET_TREE.md` (note gaps)

### Pattern

- [ ] Phase 4 branches `contracts defined` or `deferred`
- [ ] Dependency rules in `TARGET_TREE.md` hold

### Wiring

- [ ] Phase 5 tasks done or deferred
- [ ] Callers use Phase 4 contracts across boundaries

### Diagnosis

- [ ] Re-scan `DIAGNOSIS.md` findings — resolved vs remaining debt

### Quality gates

- [ ] Build: [command]
- [ ] Test: [command]
- [ ] Lint / static analysis: [command]

## Outcome

- **Resolved:** [finding IDs, branches, wiring tasks]
- **Deferred:** [with reason]
- **Follow-ups:** [out of scope work]

## Exit criteria

- [ ] `PROGRESS.md` **Outcome** written
- [ ] User summary presented

## Revision log

| Date | Change |
|------|--------|
| [YYYY-MM-DD] | Initial plan |
```
