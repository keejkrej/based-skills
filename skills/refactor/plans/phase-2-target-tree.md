# Phase 2 Plan Template

Copy into `.refactor/plans/phase-2-target-tree.md` before executing Phase 2.

```markdown
# Phase 2 — Target tree

## Meta

- **Based on:** `.refactor/DIAGNOSIS.md` ([revision])
- **Date:** [YYYY-MM-DD]

## Pattern choice

- **Target pattern:** [MVC, MVVM, MVP, layered, hexagonal, clean/onion, modular monolith, hybrid]
- **Rationale:** [why this pattern fits diagnosis]
- **Hybrids:** [subtrees using different patterns, if any]

## Work items

- [ ] Map each diagnosis cluster to a target path
- [ ] Draft annotated target tree
- [ ] Build role map (Phase 4 + Phase 5 columns)
- [ ] Write dependency rules
- [ ] List new units to introduce
- [ ] Document unchanged and deferred layout
- [ ] Resolve open decisions with user

## Outputs

- **Deliverable:** `.refactor/TARGET_TREE.md`
- **Template:** target-tree-template.md

## Exit criteria

- [ ] Every critical/high finding maps to target path
- [ ] Role map lists every Phase 4 branch/leaf
- [ ] User confirmed layout (unless target-tree-only)

## Revision log

| Date | Change |
|------|--------|
| [YYYY-MM-DD] | Initial plan |
```
