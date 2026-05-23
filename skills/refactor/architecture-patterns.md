# Architecture Patterns

Phase 1 — infer intended pattern from docs, layout, or framework defaults. Compare actual dependency flow. Do not impose an unchosen pattern.

## Use

- Record intended pattern (or hybrid) in diagnosis.
- Map modules to roles; trace dependency direction.
- File findings when boundaries or dependency rules break.

## Patterns

| Pattern | Dependency rule |
|---------|-----------------|
| **MVC** | View and controller → model; model independent of delivery |
| **MVVM** | View → view-model → model; model unaware of presentation |
| **MVP** | Presenter → view contract + model; passive view |
| **Layered** | Outer → inner only; domain has no outward deps |
| **Hexagonal** | Adapters → ports → core; core defines port abstractions |
| **Clean / onion** | Dependencies point inward; inner rings define interfaces |
| **Modular monolith** | Features → shared kernel / platform; no cross-feature internals |

## Violations (any pattern)

- Inner role imports outer or delivery/infrastructure concretions
- Business rules in presentation or entry points
- Ports shaped like a specific vendor API
- Docs name roles but code crosses boundaries freely
- Two patterns in one area with no documented split
- Pattern in one subtree only — partial or accidental adoption

Record findings: intended pattern, actual flow, locations. Layout fixes belong in `TARGET_TREE.md`, not diagnosis.
