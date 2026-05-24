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

Extract code using a **language-appropriate split** (pick what fits; no pattern required):

| Language | Common splits |
|----------|---------------|
| Python | mixin class, sibling module, package subfolder, thin re-export in original file |
| TypeScript / JS | module split, barrel `index`, colocated `*.types.ts` / `*.utils.ts` |
| Rust | `mod` file split, trait in own file, `impl` block move |
| Go | same-package file split, struct + methods across files, embedding |
| Java / Kotlin | class split, interface extract, Kotlin `interface` + default methods |
| C# | `partial class`, nested type extract, extension method file |
| C++ | header/source split, free functions file, mixin via composition |
| Ruby | `module` / concern include, file split under same namespace |
| Swift | `extension` in sibling file, protocol + conformances split |

**Prefer intact split** when the language supports it — mixin, trait, partial, extension: new sibling file, original file keeps the public surface or re-exports.

**Physical split** when not — cut section → new file; update imports so callers still resolve.

**Leave OK:** duplicate glue, parallel near-copies, `# TODO` — fix later if the user asks.

**Do not:** rename public API for aesthetics, dedupe aggressively, impose MVVM/MVC/layers, create empty abstractions.

## Artifacts

| Path | When |
|------|------|
| `plans/*` | Before each phase |
| `DIAGNOSIS.md` | Phase 1 |
| `TARGET.md` | Phase 2 |
| `PROGRESS.md` | Phase 3 |

## Done

Summarize slices completed in `PROGRESS.md`. Stop. Architecture choices are up to the user.
