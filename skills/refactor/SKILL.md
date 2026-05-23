---
name: refactor
description: >-
  Runs a language-agnostic six-phase codebase refactor with per-phase plans in
  `.refactor/plans/`. Phases: diagnose, target tree, separate (layout only),
  pattern align (APIs/signals only), wire, verify. Use when refactoring any
  codebase or when the user asks for a refactor strategy.
---

# Refactor

Six-phase structural cleanup. Use **bestpractice** for generic structure rules. Use **refactor-qtpy** with this skill for QtPy / PySide / PyQt MVVM target layout and layer boundaries. **Read only guides for the active phase** — do not load every reference up front.

## Rules

- After each **step** in a phase finishes, run **commit and push** (include `.refactor/` artifacts and any code changes for that step).
- Before each phase, write a short plan under `.refactor/plans/` (any format); track status in `PROGRESS.md`.
- No production code changes until Phase 1 is confirmed.
- Stop after each phase for user OK unless they scoped one phase only.
- Phases 3 → 4 → 5: **layout → contracts → connections**.
- Behavior-preserving by default; behavior changes need explicit approval.

## Artifacts

| Path | When |
|------|------|
| `plans/*` | One improvised plan per phase before executing |
| `DIAGNOSIS.md` | Phase 1 deliverable |
| `TARGET_TREE.md` | Phase 2 deliverable |
| `PROGRESS.md` | Live status from Phase 3 onward |

## Phases

| # | Phase | Focus |
|---|-------|--------|
| 1 | Diagnose | Anti-patterns with path-level evidence; no code moves |
| 2 | Target tree | Pattern-aligned tree, roles, current → target mapping |
| 3 | Separate | Physical layout only; wiring may break |
| 4 | Pattern align | Branches match role; APIs/signals at edges only |
| 5 | Wire | Connect via Phase 4 contracts |
| 6 | Verify | Layout, findings, build/tests; record outcome |

## Active phase — read these only

| Phase | Guides |
|-------|--------|
| 1 | [anti-patterns.md](anti-patterns.md), [architecture-patterns.md](architecture-patterns.md) |
| 2 | [architecture-patterns.md](architecture-patterns.md) |
| 3 | [separation-tactics.md](separation-tactics.md) |
| 4 | [branch-refactor.md](branch-refactor.md) |
| 5 | [wiring.md](wiring.md) |
| 6 | — |

### QtPy MVVM codebases

Also read **refactor-qtpy** during Phases 1–5 for target paths (`models/`, `viewmodels/`, `views/`), import rules, violation checklist, and wiring order.

## Scope change

- Update affected plans and deliverables; re-run from the impacted phase.
