# Anti-Pattern Catalog

Language-agnostic checks for Phase 1 diagnosis. Map signals to the project's module system, build layout, and naming conventions.

For stack-specific fix patterns, read **bestpractice** or the repo's documented standards.

For MVC, MVVM, MVP, layered, hexagonal, clean/onion, and modular-monolith boundaries, read [architecture-patterns.md](architecture-patterns.md).

## Structure and cohesion

| Anti-pattern | Signals |
|--------------|---------|
| God unit | One module owns unrelated features; many unrelated dependencies; multiple reasons to change |
| Layer violation | Presentation depends on persistence; domain depends on delivery framework; entry points contain business rules |
| Feature bleed | Changes in feature A require editing feature B without a shared contract |
| Barrel abuse | A re-export surface exposes unrelated subsystems |
| False shared | Code in a shared area used by only one consumer |
| Comments as architecture | Large section markers instead of real module boundaries |

## Duplication

| Anti-pattern | Signals |
|--------------|---------|
| Parallel implementations | Same validation, mapping, or transform copied with tiny variations |
| Contract drift | Duplicate types, schemas, or interfaces that diverged over time |
| Copy-paste wiring | Same registration, routing, or bootstrap setup repeated manually |

## Presentation and state

| Anti-pattern | Signals |
|--------------|---------|
| Reinvented infrastructure | Custom behavior where the stack already provides an equivalent primitive |
| Layout-shaped state | State named after screen regions instead of domain concepts |
| Lifecycle soup | One unit mixing subscriptions, persistence, sync, and side effects |
| Update granularity mismatch | High-frequency input drives expensive work on every change |

## Wiring and entry points

| Anti-pattern | Signals |
|--------------|---------|
| Mixed registration styles | Central dispatch, convention discovery, and one-off wiring in the same layer |
| Fat entry points | Handlers, commands, or main modules with domain logic inline |
| Orphan modules | Units nothing references; dead exports after partial refactors |

## Packages and build layout

| Anti-pattern | Signals |
|--------------|---------|
| Source vs artifact confusion | Fix in source but consumers load stale build or generated output |
| Missing rebuild in dev | Library change not picked up by dependent dev workflow |
| Leaky public boundary | Consumers reach internal paths instead of the declared public API |

## Tests and verification

| Anti-pattern | Signals |
|--------------|---------|
| Missing characterization | Refactor targets with no tests and no documented manual verification path |
| Test–source coupling | Tests depend on private internals that will break on extract |

## Scan order

1. Entry points (main, servers, CLI, jobs, plugins, bootstrap)
2. Largest units and highest fan-in modules
3. Cross-feature dependencies and shared-area usage
4. Duplicate symbol names and parallel folder trees
5. Declared public API vs actual consumer import sites
6. Test layout relative to production modules

## Language-specific checks

Only add when the diagnosed codebase uses that stack. Prefer **bestpractice** for known rules instead of duplicating them here.

- Note the language, framework, and build tool in diagnosis **Meta**.
- Record stack-specific smells as ordinary findings with evidence, not as universal anti-patterns.
