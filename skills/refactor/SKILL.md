---
name: refactor
description: >-
  Three-phase file splitting: diagnose god files, propose a target file tree (and
  structure outline from AST/symbols), then move code into smaller files while
  keeping the app runnable. No pattern enforcement. Use when breaking up large
  files or reducing god units.
---

# Refactor

Split god files into smaller ones. No MVVM, no layer enforcement, no architecture invention — just diagnose, plan, move. Use **bestpractice** for cohesion when unsure.

1. **Diagnose** — find god files and smells; no code moves
2. **Target** — propose file tree + structure outline; user confirms
3. **Move** — split and relocate; **keep the app runnable** after each slice

## Rules

- After each **step**, **commit and push** (`.refactor/` artifacts + code).
- Write a short plan under `.refactor/plans/` before each phase; track in `PROGRESS.md`.
- No production code changes until Phase 2 is confirmed.
- Stop after each phase for user OK unless they scoped one phase only.
- Behavior-preserving; no algorithm or UX changes.
- One slice = one god file or one logical chunk; verify run/build/test after each slice when practical.
- Fix imports and exports **only as needed** to keep the app runnable — no opportunistic rewiring.

## Phase 1 — diagnose

Find the pain: largest files, highest fan-in modules, unrelated concerns mixed together, section comments used as fake structure.

Record in `DIAGNOSIS.md`: path, LOC (approx), what is mixed (UI + I/O + rules, etc.), and why it is a split candidate.

Scan order: entry points → biggest files → cross-feature imports → duplicate logic.

## Phase 2 — target

Propose where pieces should land. Group by **concern or feature**, not by architecture pattern.

Deliverables in `TARGET.md`:
- **File tree** — current path → target path for each chunk
- **Structure outline** — per god file: classes, functions, modules (AST or symbol tree); mark which target file each block moves to

Use the outline to pick cut points inside large files. A module dependency graph is optional only when import cycles or cross-file coupling are the main risk.

User confirms before Phase 3. Adjust the file tree if the outline exposes awkward splits.

## Phase 3 — move

**No-harm splits only** — same scope, same public API, re-import/include back. No new interfaces, barrels, traits-as-design, DI reshaping, or abstractions.

| Language | Safe splits |
|----------|-------------|
| Python | mixin (instance methods on `self`); sibling module for pure/static/class helpers only |
| TypeScript / JS | sibling module + import; colocated `*.utils.ts` / helpers file |
| Rust | `foo.rs` + `foo/bar.rs` child modules; `impl` block in submodule — no new `mod.rs` (**bestpractice**) |
| Go | same-package file split; methods on same struct across files |
| Java | same-package helper class; god class delegates — no new interfaces |
| Kotlin | same-package helper class; `extension` fns in sibling file |
| C# | `partial class` across files |
| C++ | free functions / helpers in sibling `.cpp` (same namespace) |
| Ruby | `module` include + sibling file |
| Swift | `extension` in sibling file |

**Default:** sibling file + import/include back for code that does not need `self` / instance attachment. For god **classes**, use language attach patterns (Python mixin, C# partial, Swift/Kotlin extension, Ruby module).

**Leave OK:** duplicate glue, parallel near-copies, `# TODO` — fix later if the user asks.

**Do not:** rename public API, dedupe aggressively, new interfaces or traits, barrel `index` rewires, impose MVVM/MVC/layers, nested type extracts that change visibility.

## Artifacts

| Path | When |
|------|------|
| `plans/*` | Before each phase |
| `DIAGNOSIS.md` | Phase 1 |
| `TARGET.md` | Phase 2 |
| `PROGRESS.md` | Phase 3 |

## Done

Summarize slices completed in `PROGRESS.md`. Stop. Architecture choices are up to the user.
