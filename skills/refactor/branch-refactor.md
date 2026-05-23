# Pattern Align

Phase 4 — one branch/leaf from `TARGET_TREE.md` **Role map** at a time. Match pattern role; publish boundary contracts only.

## Walk order

1. Inner / core — ports and pure rules
2. Application / use cases — orchestration types only
3. Infrastructure — adapter shapes against ports; no bootstrap binding
4. Presentation — commands, handler signatures, delivery-facing signals
5. Bootstrap — registration slots only; no implementation wiring

Deepest leaves first within a branch. Modular monolith: finish one feature branch before crossing boundaries.

## Per branch

- Inventory files, Phase 3 glue, broken refs — do not wire yet.
- Evict wrong-role code; move to correct branch if needed.
- Replace cross-branch concrete deps with port or contract types.
- Apply **bestpractice** inside the branch — one responsibility per file.
- Publish contracts at the branch edge: ports, public API, commands, events, handler signatures.
- Use compile stubs only when needed; mark for Phase 5.
- Done when status is `contracts defined`.
- **Re-steer:** if wrong-role code keeps appearing in the same branch, pause — update `DIAGNOSIS.md` / `TARGET_TREE.md` and fix the branch pattern before aligning the next branch the same way.
- **Defer OK:** copy-paste, mixins, and parallel near-duplicates inside a branch during early alignment — record and dedupe once contracts are stable.

## Role fit

| Check | Action |
|-------|--------|
| Wrong pattern role | Move to correct branch |
| Inner imports outer | Port or abstraction; wire in Phase 5 |
| Rules in presentation layer | Extract to application or domain branch |
| Persistence/transport in core | Impl to infrastructure; core keeps port type |

## Phase 4 only

- Internal shape and contract **definition**
- Replace concrete cross-branch imports with abstractions

## Defer to Phase 5

- Import graph, DI, routes, CLI, jobs, events
- Remove Phase 3 glue after connections exist
- End-to-end integration tests

## Do not

- Connect callers across branches
- Register DI, routes, or event handlers
- Add features or change behavior without approval

Branch status: `pending` · `in progress` · `contracts defined` · `blocked` · `deferred`
