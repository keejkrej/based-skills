# Branch Refactor Guide

Use in **Phase 4 — Pattern align**. Work **one leaf or branch** per `plans/phase-4-pattern-align.md` until each path matches its pattern role and exposes **boundary APIs/signals only**.

Phase 3 placed code in the right folders. Phase 4 makes each folder **correct on the inside** and defines **how other branches may talk to it** — without connecting callers yet (Phase 5).

## Walk order

Derive the checklist from **Role map** in `TARGET_TREE.md`. Default order:

1. **Inner / core leaves** — domain entities, pure rules; define **ports** (interfaces) outward needs
2. **Application / use-case branches** — orchestration; define use-case inputs/outputs and port dependencies as types only
3. **Infrastructure branches** — declare adapter **implements** relationships against ports (skeleton ok); no bootstrap binding yet
4. **Presentation branches** — views, view models, controllers; define commands, props, handler signatures, UI-facing signals
5. **Bootstrap / composition root** — declare registration **slots** or module interfaces only; do not register implementations here in Phase 4

Within a branch, go **deepest leaves first**, then roll up to the branch root.

For **modular monolith** layouts, finish one feature branch end-to-end before crossing feature boundaries unless `TARGET_TREE.md` says otherwise.

## One branch iteration

For path `[branch-or-leaf]` with pattern role `[role]`:

### 1. Inventory

- List files and symbols under the path.
- Note Phase 3 glue (stubs, re-exports, duplicates) still present.
- Note broken cross-branch references from `PROGRESS.md` **Separation** — do not fix yet.

### 2. Role fit

Compare contents to the role definition in [architecture-patterns.md](architecture-patterns.md) and **Role map** in `TARGET_TREE.md`.

| Check | Action if failed |
|-------|------------------|
| Code belongs to another pattern role | Move to the correct branch (update `TARGET_TREE.md` if unplanned) |
| Direct import of outer layer from inner | Replace with a port/interface the outer layer will implement in Phase 5 |
| Presentation logic in domain | Extract upward to application or presentation branch |
| Concrete persistence/transport in core | Move impl to infrastructure; core keeps port type only |

### 3. Internal shape

Apply **bestpractice** within the branch:

- One clear responsibility per file.
- No unrelated concerns left from Phase 3 paste.
- Dedupe parallel helpers inside the branch.
- Thin entry files; owned modules for rules and data shapes.

### 4. Define boundary API / signals

Publish the branch's **outward contract** without wiring callers:

| Artifact | Examples |
|----------|----------|
| Port / interface | Repository, gateway, clock, notifier |
| Public module API | Exported functions, facades, package entry |
| Command / query | Use-case input/output types |
| Signal / event | Domain events, UI events, message names |
| Handler signature | Controller action, route handler type, job payload |
| View contract | ViewModel surface, props schema, binding model |

Rules:

- Contracts live at the branch **edge** (dedicated port file, public index, handler module).
- Inner code depends on **abstractions**, not sibling branches' concrete files.
- Use stubs, unimplemented adapters, or `todo!()` / throw placeholders only when needed to compile — mark for Phase 5.
- **Do not** connect imports, DI, routes, or event subscriptions across branches in Phase 4.
- Record each contract in `PROGRESS.md` under the branch entry.

### 5. Verify branch (local)

- Branch internals typecheck/build in isolation or with mock boundaries.
- Contracts are documented and discoverable at the branch edge.
- Mark branch `contracts defined` in `PROGRESS.md` when role, internal shape, and boundary APIs/signals are complete.

## Branch status values

| Status | Meaning |
|--------|---------|
| `pending` | Not started |
| `in progress` | Active refactor |
| `contracts defined` | Pattern role honored; boundary APIs/signals published |
| `blocked` | Needs `TARGET_TREE.md` or user decision |
| `deferred` | Explicitly out of scope |

## What belongs in Phase 4 vs Phase 5

**Phase 4 — in scope**

- Internal refactor to match pattern role
- Cross-branch moves required by pattern rules
- Port, interface, signal, and public API **definition**
- Replacing concrete cross-branch imports with contract types

**Phase 5 — defer to wiring**

- Import graph fixes across branches
- DI registration and adapter binding
- Route tables, CLI registration, job wiring
- Event subscribe/publish connections
- Removing Phase 3 glue once real connections exist
- End-to-end build and integration tests

**Out of scope (both phases without approval)**

- New features or behavior changes
- Re-opening deferred diagnosis clusters

## Hybrid and feature-local patterns

When `TARGET_TREE.md` marks a subtree as a different pattern:

- Use the **local pattern** for that subtree's Phase 4 checklist.
- Define cross-subtree contracts at the **parent branch** before children.

## Record per branch in PROGRESS.md

```markdown
### `[path]` — [pattern role]

- **Status:** contracts defined | in progress | pending | blocked | deferred
- **Finding IDs:** F2, F5
- **Moved out:** [code sent to other branches]
- **Contracts defined:** [ports, signals, public APIs at this edge]
- **Awaiting wire (Phase 5):** [callers/adapters that must connect]
- **Glue remaining:** [stubs left for Phase 5]
- **Local verification:** [commands run]
- **Notes:** [blockers, follow-ups]
```
