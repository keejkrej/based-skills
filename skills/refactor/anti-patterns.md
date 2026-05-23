# Anti-Patterns

Phase 1 scan checklist. Map signals to the repo's module layout. Stack-specific fixes: **bestpractice**. Pattern boundaries: [architecture-patterns.md](architecture-patterns.md).

## Structure

| Anti-pattern | Signals |
|--------------|---------|
| God unit | Unrelated features in one owner; many unrelated dependencies |
| Layer violation | Presentation → persistence; domain → delivery framework; rules in entry points |
| Feature bleed | Feature A change requires editing feature B internals |
| Barrel abuse | Re-export surface mixes unrelated subsystems |
| False shared | Shared area serves only one consumer |
| Comments as architecture | Section markers instead of modules |

## Duplication

| Anti-pattern | Signals |
|--------------|---------|
| Parallel implementations | Same logic copied with tiny variations |
| Contract drift | Duplicate types or schemas that diverged |
| Copy-paste wiring | Repeated registration or bootstrap setup |

## Presentation and state

| Anti-pattern | Signals |
|--------------|---------|
| Reinvented infrastructure | Custom behavior where the stack has a built-in equivalent |
| Layout-shaped state | State named after screen regions, not domain |
| Lifecycle soup | Subscriptions, persistence, sync, side effects in one unit |
| Update granularity mismatch | High-frequency input triggers expensive work every tick |

## Wiring

| Anti-pattern | Signals |
|--------------|---------|
| Mixed registration styles | Dispatch, convention discovery, and one-off wiring mixed |
| Fat entry points | Handlers or main modules with domain logic inline |
| Orphan modules | Unreferenced units; dead exports |

## Build layout

| Anti-pattern | Signals |
|--------------|---------|
| Source vs artifact confusion | Fix in source; consumers load stale build output |
| Missing rebuild in dev | Dependent workflow does not pick up library changes |
| Leaky public boundary | Consumers bypass declared public API |

## Tests

| Anti-pattern | Signals |
|--------------|---------|
| Missing characterization | Refactor target has no tests or verification path |
| Test–source coupling | Tests depend on private internals |

## Scan order

1. Entry points → largest / highest fan-in units → cross-feature deps → duplicate symbols → public API vs actual use → test layout

Record stack-specific smells as ordinary findings with evidence — not universal anti-patterns.
