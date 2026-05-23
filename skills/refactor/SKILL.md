---
name: refactor
description: >-
  Runs a language-agnostic six-phase codebase refactor with per-phase plans in
  `.refactor/plans/`. Phases: diagnose, target tree, separate (layout only),
  pattern align (APIs/signals only), wire, verify. Use when refactoring any
  codebase or when the user asks for a refactor strategy.
---

# Refactor

Six-phase structural cleanup. Use **bestpractice** for generic structure rules. Use **refactor-qtpy** with this skill for QtPy / PySide / PyQt MVVM target layout and layer boundaries. **Read guides for the active phase**; during Phases 3–5 also re-check Phase 1–2 artifacts and **refactor-qtpy** checklists per slice — do not load every reference up front.

## Rules

- After each **step** in a phase finishes, run **commit and push** (include `.refactor/` artifacts and any code changes for that step).
- Before each phase, write a short plan under `.refactor/plans/` (any format); track status in `PROGRESS.md`.
- No production code changes until Phase 1 is confirmed.
- Stop after each phase for user OK unless they scoped one phase only.
- Phases 3 → 4 → 5: **layout → contracts → connections**.
- Behavior-preserving by default; behavior changes need explicit approval.
- **Continuous steering:** during Phases 3–5, regularly re-check **Phase 1** (`DIAGNOSIS.md`, anti-patterns) and **Phase 2** (`TARGET_TREE.md`, role map). Refine those artifacts when new smells appear — do not treat them as frozen after the first pass. **Stop immediately** on smells that would spread the wrong pattern; **tolerate or defer** simpler smells when that keeps Phase 3 correct and low-risk (see below).

## Continuous steering (Phases 3–5)

Phase 1 and 2 are **living references**, not one-time gate checks.

**When to re-check:** after each slice in Phase 3, each branch in Phase 4, and each wiring edge in Phase 5 — especially when touching a new feature or component.

**What to check:**
- **Phase 1:** does new/moved code match a recorded anti-pattern or reveal a new one? Add path-level evidence to `DIAGNOSIS.md`.
- **Phase 2:** does the file land in the right role/path? Update `TARGET_TREE.md` and the current → target mapping if the tree was wrong or incomplete.

**When to pause and steer:** if a slice would **spread** a smell (wrong layer, cross-feature direct calls, god unit, contract skip) to the next component — stop, fix the pattern or update Phase 1–2 artifacts, then continue. Do not batch “fix later” across many files.

**Tolerate / defer (simplicity + correctness):** during Phase 3 especially, **correctness and a working move beat premature cleanup**. These are OK to leave temporarily — record as `deferred` in `DIAGNOSIS.md` or `PROGRESS.md` with a target phase (usually 4 or 5):
- Copy-paste or lift-and-shift when dedupe would risk behavior or wiring during the move
- Mixins, traits, partial types, intact splits, and minimal glue files
- Parallel implementations with tiny variations
- `# TODO wire` stubs and broken refs expected until Phase 5

Do **not** defer smells that teach the wrong shape for the next component (e.g. view→model import, skipping viewmodel, direct cross-view calls) — those are cheap to copy and expensive to unwind.

**QtPy MVVM:** re-run **refactor-qtpy** violation checklist on each new triplet before using it as the template for the next — but defer duplicate glue or copy-paste within a slice when it keeps Phase 3 safe; dedupe and contract cleanup belong in Phases 4–5.

## Artifacts

| Path | When |
|------|------|
| `plans/*` | One improvised plan per phase before executing |
| `DIAGNOSIS.md` | Phase 1 deliverable; **update during Phases 3–5** when new anti-patterns appear |
| `TARGET_TREE.md` | Phase 2 deliverable; **update during Phases 3–5** when roles or paths need refinement |
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
