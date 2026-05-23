# Wiring

Phase 5 — connect one integration edge at a time through Phase 4 contracts.

## Before starting

- In-scope branches are `contracts defined` or `deferred` in `PROGRESS.md`.

## Walk order

1. Inner / core — no outward imports
2. Infrastructure — bind adapters to ports
3. Application — inject ports; use presentation APIs only
4. Presentation — bind to Phase 4 surfaces
5. Bootstrap — composition root last

## Per edge

- Identify caller path, callee contract, mechanism (import, DI, route, event, config).
- Point caller at published contract, not internal files.
- Register bindings in bootstrap or feature module.
- Replace `# TODO wire` and Phase 3 stubs; delete duplicate paths.
- Verify smallest build/test scope that crosses the boundary.
- **Re-steer:** if wiring exposes a layer violation or ad-hoc cross-component call, stop — record in `DIAGNOSIS.md`, adjust contracts or target tree, fix this edge before wiring the rest the same way.

## Rules

- Do not bypass Phase 4 contracts.
- Do not change contract shapes without updating Phase 4 artifacts.
- Do not move code between branches — back to Phase 3 or 4.
- Prefer composition root over scattered singletons.
- Obey `TARGET_TREE.md` dependency rules.
- Behavior-preserving unless user approved otherwise.

## Done when

- Scope builds; tests pass or failures documented
- No `# TODO wire` or Phase 3 stubs in scope
- Cross-branch calls use Phase 4 contracts only
