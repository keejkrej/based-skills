# Separation

Phase 3 — move cohesive code to `TARGET_TREE.md` paths. Layout only; no cross-branch wiring.

## Rules

- One slice = one PR-sized move; one diagnosis cluster per slice.
- Match target paths; do not invent folders mid-slice.
- No behavior changes, symbol renames, dedupe, or test fixes.
- Do not fix imports, exports, registration, DI, routes, or config wiring.
- Red build after a slice is OK; record breaks in `PROGRESS.md`.
- **Re-steer:** before starting the next slice, re-check `DIAGNOSIS.md` and `TARGET_TREE.md` — if this slice exposed a wrong **role or architectural pattern**, update those artifacts and fix before copying it to the next component.
- **Defer OK in Phase 3:** copy-paste, mixins, intact splits, glue stubs, and skipped dedupe — when they keep the slice simple and avoid breaking behavior mid-move. Record as `deferred` with target phase 4 or 5; clean up there.

## Intact split

Use when the stack merges multiple physical files into one logical unit without changing call sites.

- Same logical module split across sibling files under one namespace or package
- Partial type, mixin, trait, extension, or include-based composition
- Leave minimal glue in the original file; defer cleanup to Phases 4–5

## Physical split

Use when intact split is unavailable.

- Cut-and-paste or lift-and-shift to target path
- Extract section → file or cluster → folder
- Leave stubs or `# TODO wire`; broken refs are expected

## Do not

- Rename public symbols for aesthetics
- Change algorithms or control flow
- Dedupe, rewire, or collapse target folders because wiring is hard
