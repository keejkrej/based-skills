# Wiring Guide

Use in **Phase 5 — Wire**. Phase 4 left **APIs and signals** at branch boundaries; execute tasks from `plans/phase-5-wire.md` to **connect** them.

## Goal

Make the separated, pattern-aligned codebase **runnable and integrated**:

- Imports and exports resolve across branches
- Ports have adapter implementations bound
- DI, service locators, or composition roots register dependencies
- Routes, CLI commands, jobs, and event handlers reach the right modules
- Config and build scripts reference final paths
- Phase 3 glue and temporary stubs removed
- Tests and callers use the published contracts from Phase 4

## Prerequisites

- Every in-scope branch in `PROGRESS.md` **Pattern align** is `contracts defined` or `deferred`
- `plans/phase-5-wire.md` tasks are ready to execute

## Walk order

1. **Inner/core** — ensure nothing imports outward; contracts face inward only
2. **Infrastructure adapters** — bind implementations to core/application ports
3. **Application / use cases** — inject ports; call presentation-facing signals only through defined APIs
4. **Presentation** — bind to view models, controllers, or handlers via Phase 4 surfaces
5. **Bootstrap / composition root** — central registration, module loading, plugin discovery last

Wire **one integration edge at a time** — a port binding, a route table, a DI module, an event subscription — not whole-repo import sweeps unless the repo is small.

## One wiring task

### 1. Identify the edge

- **From:** caller branch/path
- **To:** callee contract from Phase 4 (port, interface, handler, signal, public module API)
- **Mechanism:** import, DI token, event bus, HTTP route, message queue, config key

### 2. Connect

- Point caller at the **published contract**, not internal files
- Register adapter → port binding in bootstrap or feature module
- Replace `# TODO wire` and Phase 3 stubs with real references
- Delete duplicate code paths once the wired path is active

### 3. Verify edge

- Build/typecheck the smallest scope that includes caller + callee
- Run tests that cross this boundary
- Mark task done in `PROGRESS.md` **Wiring**

## Wiring rules

- **Do not** change Phase 4 contract shapes unless integration proves them wrong — update the contract deliberately, then re-wire
- **Do not** move code between branches — send layout fixes back to Phase 3 or pattern fixes to Phase 4
- Default to **behavior-preserving** connections
- Prefer composition root / bootstrap for cross-cutting bindings over scattered singletons
- Obey **Dependency rules** in `TARGET_TREE.md`

## Common wiring work

| Task | Examples |
|------|----------|
| Module graph | Fix imports/exports, package `exports`, module paths |
| DI / composition | Container registration, factory wiring, constructor injection |
| Delivery | HTTP routes, CLI subcommands, RPC handlers, message consumers |
| Events / signals | Subscribe, publish, connect callbacks to defined handlers |
| Persistence | Bind repository port to database adapter |
| Config / build | Update paths in bundler, CI, env templates, code generation |
| Tests | Point tests at public APIs; remove imports of moved internals |

## Record in PROGRESS.md

```markdown
## Wiring

### W[N] — [caller] → [contract]

- **Status:** done | in progress | pending | blocked
- **Branch paths:** `[from]` → `[to]`
- **Contract:** [port / interface / signal name from Phase 4]
- **Mechanism:** [DI, import, route, event, config]
- **Glue removed:** [stubs, duplicates]
- **Verification:** [commands run]
- **Notes:** [blockers]
```

## Done when

- Project builds for the refactor scope
- Tests pass (or failures documented as pre-existing)
- No remaining `# TODO wire` or Phase 3 temporary stubs in scope
- Callers use Phase 4 contracts only across branch boundaries
