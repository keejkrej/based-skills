# Phase Plans

Each refactor phase gets its own plan under `.refactor/plans/` in the **target repo**. Write the plan **before** executing that phase; track live status in `PROGRESS.md`.

## Layout

```
.refactor/
├── DIAGNOSIS.md          # Phase 1 deliverable
├── TARGET_TREE.md        # Phase 2 deliverable
├── PROGRESS.md           # execution status (all phases)
└── plans/
    ├── phase-1-diagnose.md
    ├── phase-2-target-tree.md
    ├── phase-3-separate.md
    ├── phase-4-pattern-align.md
    ├── phase-5-wire.md
    └── phase-6-verify.md
```

## Rules

- One plan file per phase — do not merge phases into a single plan.
- Plans are **intent and checklist**; deliverables (`DIAGNOSIS.md`, `TARGET_TREE.md`, code changes) stay separate.
- When a phase scope changes, update that phase's plan and add a **Revision log** entry.
- Skill templates live in this folder; copy content into the target repo `.refactor/plans/`.

## Templates

| Phase | Plan file | Template |
|-------|-----------|----------|
| 1 | `phase-1-diagnose.md` | [phase-1-diagnose.md](phase-1-diagnose.md) |
| 2 | `phase-2-target-tree.md` | [phase-2-target-tree.md](phase-2-target-tree.md) |
| 3 | `phase-3-separate.md` | [phase-3-separate.md](phase-3-separate.md) |
| 4 | `phase-4-pattern-align.md` | [phase-4-pattern-align.md](phase-4-pattern-align.md) |
| 5 | `phase-5-wire.md` | [phase-5-wire.md](phase-5-wire.md) |
| 6 | `phase-6-verify.md` | [phase-6-verify.md](phase-6-verify.md) |
